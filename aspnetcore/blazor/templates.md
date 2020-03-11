---
title: Šablony ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o ASP.NET Core Blazor šablonách aplikací a Blazor struktuře projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: acfa4b8a42cbd310c6fc6dc973573578e94ef999
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664239"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>Šablony ASP.NET Core Blazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Rozhraní Blazor Framework poskytuje šablony pro vývoj aplikací pro každý z Blazorch modelů hostování:

* Blazor WebAssembly (`blazorwasm`)
* Server Blazor (`blazorserver`)

Další informace o modelech hostování Blazornaleznete v tématu <xref:blazor/hosting-models>.

Podrobné pokyny k vytvoření aplikace Blazor ze šablony naleznete v tématu <xref:blazor/get-started>.

## <a name="opno-locblazor-project-structure"></a>Blazor strukturu projektu

Následující soubory a složky tvoří aplikaci Blazor vygenerovanou z šablony Blazor:

* *Program.cs* &ndash; vstupním bodem aplikace, který nastaví:

  * [Hostitel](xref:fundamentals/host/generic-host) ASP.NET Core (Blazor Server)
  * Hostitel WebAssembly (Blazor WebAssembly) &ndash; kód v tomto souboru je jedinečný pro aplikace vytvořené ze šablony Blazor WebAssembly (`blazorwasm`).
    * Komponenta `App`, která je kořenovou komponentou aplikace, je zadána jako `app` elementu DOM do metody `Add`.
    * Služby je možné konfigurovat pomocí metody `ConfigureServices` v Tvůrci hostitele (například `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).
    * Konfiguraci lze dodávat prostřednictvím tvůrce hostitele (`builder.Configuration`).

* *Startup.cs* (Blazor Server) &ndash; obsahuje logiku spouštění aplikace. Třída `Startup` definuje dvě metody:

  * `ConfigureServices` &ndash; konfiguruje služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. V aplikacích Blazor Server se služby přidávají voláním <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>a `WeatherForecastService` se přidá do kontejneru služby pro použití v ukázkové součásti `FetchData`.
  * `Configure` &ndash; nakonfiguruje kanál zpracování žádostí aplikace:
    * k nastavení koncového bodu pro připojení v reálném čase pomocí prohlížeče se volá <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>. Připojení se vytvoří pomocí [SignalR](xref:signalr/introduction), což je rozhraní, které umožňuje přidávat do aplikací webové funkce v reálném čase.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) se zavolá, aby se nastavila Kořenová stránka aplikace (*pages/_Host. cshtml*) a povolila se navigace.

* *wwwroot/index.html* (Blazor webové sestavení) &ndash; kořenovou stránku aplikace implementované jako stránka HTML:
  * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
  * Stránka určuje, kde je vygenerována kořenová `App` komponenta. Komponenta `App` (*App. Razor*) je zadána jako `app` elementu modelu DOM do metody `AddComponent` v `Startup.Configure`.
  * Načte se soubor `_framework/blazor.webassembly.js` JavaScript, který:
    * Stáhne rozhraní .NET runtime, aplikaci a závislosti aplikace.
    * Inicializuje modul runtime pro spuštění aplikace.

* *Pages/_Host. cshtml* (Blazor Server) &ndash; kořenové stránce aplikace implementované jako stránka Razor:
  * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
  * Soubor `_framework/blazor.server.js` JavaScript je načten, což nastaví SignalR připojení v reálném čase mezi prohlížečem a serverem.
  * Stránka hostitel Určuje, kde je vykreslena součást root `App` (*App. Razor*).

* *App. razor* &ndash; kořenovou komponentu aplikace, která nastaví směrování na straně klienta pomocí komponenty <xref:Microsoft.AspNetCore.Components.Routing.Router>. Komponenta `Router` zachycuje navigaci v prohlížeči a vykreslí stránku, která odpovídá požadované adrese.

* *Pages* &ndash; obsahuje směrovatelný komponenty/stránky ( *. Razor*), které tvoří aplikaci Blazor. Trasa pro každou stránku je určena pomocí direktivy [`@page`](xref:mvc/views/razor#page) . Šablona obsahuje následující součásti:
  * `Index` (*index. Razor*) &ndash; implementuje domovskou stránku.
  * `Counter` (*Counter. Razor*) &ndash; implementuje stránku čítače.
  * `Error` (*Error. Razor*, Blazor Server app) &ndash; vykreslený, když v aplikaci dojde k neošetřené výjimce.
  * `FetchData` (*FetchData. Razor*) &ndash; implementuje stránku načíst data.

* *Sdílená* složka &ndash; obsahuje jiné součásti uživatelského rozhraní ( *. Razor*) používané aplikací:
  * `MainLayout` (*MainLayout. Razor*) &ndash; komponentu rozložení aplikace.
  * `NavMenu` (*NavMenu. Razor*) &ndash; implementuje navigaci bočním panelem. Zahrnuje [komponentu NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), která vykresluje navigační odkazy na jiné součásti Razor. Komponenta `NavLink` automaticky indikuje vybraný stav při načtení jeho komponenty, což pomáhá uživateli pochopit, která součást se aktuálně zobrazuje.

* *_Imports. razor* &ndash; obsahuje společné direktivy Razor, které se mají zahrnout do komponent aplikace ( *. Razor*), jako jsou například direktivy [`@using`](xref:mvc/views/razor#using) pro obory názvů.

* Složka *dat* (Blazor Server) &ndash; obsahuje třídu `WeatherForecast` a implementaci `WeatherForecastService`, která poskytuje ukázková data o počasí pro `FetchData` komponentu aplikace.

* *wwwroot* &ndash; [kořenovou složku webu](xref:fundamentals/index#web-root) pro aplikaci, která obsahuje veřejné statické prostředky aplikace.

* *appSettings. JSON* (Blazor Server) &ndash; nastavení konfigurace aplikace.
