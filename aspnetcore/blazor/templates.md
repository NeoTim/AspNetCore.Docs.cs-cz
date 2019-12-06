---
title: Šablony ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o ASP.NET Core Blazor šablonách aplikací a Blazor struktuře projektu.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: bc0ea4a777e8684a7b0925377b8a19a45c2b531c
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879658"
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

* *Program.cs* &ndash; vstupním bodem aplikace, který nastaví ASP.NET Core [hostitele](xref:fundamentals/host/generic-host). Kód v tomto souboru je společný pro všechny ASP.NET Core aplikace generované ze šablon ASP.NET Core.

* *Startup.cs* &ndash; obsahuje logiku spouštění aplikace. Třída `Startup` definuje dvě metody:

  * `ConfigureServices` &ndash; konfiguruje služby [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. V aplikacích Blazor Server se služby přidávají voláním <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>a `WeatherForecastService` se přidá do kontejneru služby pro použití v ukázkové součásti `FetchData`.
  * `Configure` &ndash; nakonfiguruje kanál zpracování žádostí aplikace:
    * Blazor rozhraní WebAssembly &ndash; přidá součást `App` (zadaná jako element modelu DOM `app` do metody `AddComponent`), která je kořenovou komponentou aplikace.
    * Blazor Server
      * k nastavení koncového bodu pro připojení v reálném čase pomocí prohlížeče se volá <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>. Připojení se vytvoří pomocí [SignalR](xref:signalr/introduction), což je rozhraní, které umožňuje přidávat do aplikací webové funkce v reálném čase.
      * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) se zavolá, aby se nastavila Kořenová stránka aplikace (*pages/_Host. cshtml*) a povolila se navigace.

* *wwwroot/index.html* (Blazor webové sestavení) &ndash; kořenovou stránku aplikace implementované jako stránka HTML:
  * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
  * Stránka určuje, kde je vygenerována kořenová `App` komponenta. Komponenta `App` (*App. Razor*) je zadána jako `app` elementu modelu DOM do metody `AddComponent` v `Startup.Configure`.
  * Načte se soubor *_framework/Blazor.WebAssembly.js* JavaScriptu, který:
    * Stáhne rozhraní .NET runtime, aplikaci a závislosti aplikace.
    * Inicializuje modul runtime pro spuštění aplikace.

* *Pages/_Host. cshtml* (Blazor Server) &ndash; kořenové stránce aplikace implementované jako stránka Razor:
  * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
  * Načte se soubor *_framework/Blazor.Server.js* JavaScriptu, který nastaví SignalR připojení v reálném čase mezi prohlížečem a serverem.
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
