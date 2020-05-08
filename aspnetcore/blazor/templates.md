---
title: Šablony Blazor ASP.NET Core
author: guardrex
description: Přečtěte si Blazor o ASP.NET Core šablonách aplikací a Blazor struktuře projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 705fa32ee72221b3c18653e9f3495b9cd61e9ad1
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967425"
---
# <a name="aspnet-core-blazor-templates"></a>Šablony Blazor ASP.NET Core

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Rozhraní poskytuje šablony pro vývoj aplikací pro každý model Blazor hostování:

* BlazorWebAssembly (`blazorwasm`)
* BlazorServer (`blazorserver`)

Další informace o Blazormodelech hostování naleznete v tématu <xref:blazor/hosting-models>.

Podrobné pokyny k vytvoření Blazor aplikace ze šablony naleznete v tématu. <xref:blazor/get-started>

Možnosti šablony jsou k dispozici předáním `--help` možnosti příkazového řádku [dotnet New](/dotnet/core/tools/dotnet-new) CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorstruktura projektu

Následující soubory a složky tvoří Blazor aplikaci vygenerovanou ze Blazor šablony:

* *Program.cs* &ndash; vstupní bod aplikace, který nastaví:

  * [Hostitel](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server)
  * Hostitel WebAssembly (Blazor WebAssembly) &ndash; kód v tomto souboru je jedinečný pro aplikace vytvořené ze Blazor šablony WebAssembly (`blazorwasm`).
    * `App` Komponenta, která je kořenovou komponentou aplikace, je zadána jako prvek `app` modelu DOM pro `Add` metodu.
    * Služby lze konfigurovat pomocí `ConfigureServices` metody v Tvůrci hostitele (například `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * Konfiguraci lze dodávat prostřednictvím tvůrce hostitele (`builder.Configuration`).

* *Startup.cs* (Blazor Server) &ndash; obsahuje logiku spouštění aplikace. `Startup` Třída definuje dvě metody:

  * `ConfigureServices`&ndash; Nakonfiguruje služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. V Blazor serverových aplikacích se služby přidávají voláním <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>a `WeatherForecastService` přidá se do kontejneru služby pro použití v ukázkové `FetchData` součásti.
  * `Configure`&ndash; Nakonfiguruje kanál pro zpracování požadavků aplikace:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>je volána k nastavení koncového bodu pro připojení v reálném čase pomocí prohlížeče. Připojení se vytvoří pomocí [SignalR](xref:signalr/introduction), což je rozhraní, které umožňuje přidávat do aplikací webové funkce v reálném čase.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) se zavolá, aby se nastavila Kořenová stránka aplikace (*pages/_Host. cshtml*) a povolila se navigace.

* *wwwroot/index.html* (Blazor WebAssembly) &ndash; Kořenová stránka aplikace implementované jako stránka HTML:
  * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
  * Stránka určuje, kde je vygenerována kořenová `App` komponenta. `App` Součást (*App. Razor*) je zadána jako prvek `app` modelu DOM k `AddComponent` metodě v. `Startup.Configure`
  * Načte `_framework/blazor.webassembly.js` se soubor JavaScriptu, který:
    * Stáhne rozhraní .NET runtime, aplikaci a závislosti aplikace.
    * Inicializuje modul runtime pro spuštění aplikace.

* *App. Razor* &ndash; : kořenová komponenta aplikace, která nastaví směrování na <xref:Microsoft.AspNetCore.Components.Routing.Router> straně klienta pomocí komponenty. `Router` Komponenta zachycuje navigaci v prohlížeči a vykreslí stránku, která odpovídá požadované adrese.

* Složka &ndash; *Pages* obsahuje směrovatelný komponenty/stránky (*. Razor*), které tvoří Blazor aplikaci a kořenovou Razor stránku Blazor serverové aplikace. Trasa pro každou stránku je určena pomocí [`@page`](xref:mvc/views/razor#page) direktivy. Šablona obsahuje následující:
  * *_Host. cshtml* (Blazor Server) &ndash; kořenovou stránku aplikace implementované jako Razor stránka:
    * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
    * Načte `_framework/blazor.server.js` se soubor JavaScriptu, který nastaví připojení v reálném čase SignalR mezi prohlížečem a serverem.
    * Stránka hostitel Určuje, kde je vygenerována kořenová `App` součást (*App. Razor*).
  * `Counter`(*Čítač. Razor*) &ndash; implementuje stránku čítače.
  * `Error`(*Chyba. Razor*, Blazor pouze serverová aplikace) &ndash; Vygenerováno v případě, že v aplikaci dojde k neošetřené výjimce.
  * `FetchData`(*FetchData. Razor*) &ndash; implementuje stránku načíst data.
  * `Index`(*Index. Razor*) &ndash; implementuje domovskou stránku.

* *Sdílená* složka &ndash; obsahuje další součásti uživatelského rozhraní (*. Razor*) používané aplikací:
  * `MainLayout`(*MainLayout. Razor*) &ndash; součást rozložení aplikace
  * `NavMenu`(*NavMenu. Razor*) &ndash; implementuje navigaci bočním panelem. Zahrnuje [komponentu NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), která vykresluje navigační odkazy na jiné Razor součásti. `NavLink` Komponenta automaticky indikuje vybraný stav při načtení jeho komponenty, což pomáhá uživateli pochopit, která součást se aktuálně zobrazuje.

* *_Imports. Razor* &ndash; obsahuje společné Razor direktivy, které se mají zahrnout do komponent aplikace (*. Razor*), [`@using`](xref:mvc/views/razor#using) jako jsou například direktivy pro obory názvů.

* Složka *dat* (Blazor Server) &ndash; obsahuje `WeatherForecast` třídu a implementaci `WeatherForecastService` , která poskytuje ukázková data o počasí `FetchData` součásti aplikace.

* *wwwroot* &ndash; [Webová kořenová](xref:fundamentals/index#web-root) složka pro aplikaci, která obsahuje veřejné statické prostředky aplikace

* nastavení konfigurace *appSettings. JSON* Blazor ( &ndash; Server) pro aplikaci
