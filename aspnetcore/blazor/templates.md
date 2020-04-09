---
title: ASP.NET Blazor základní šablony
author: guardrex
description: Přečtěte si Blazor o ASP.NET Blazor šablon aplikací Core a struktuře projektů.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 71a9d9eee8637dda0b3cecac82ff96a0c3bfedb5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320983"
---
# <a name="aspnet-core-opno-locblazor-templates"></a>ASP.NET Blazor základní šablony

[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Rámec Blazor poskytuje šablony pro vývoj aplikací Blazor pro každý z hostitelských modelů:

* BlazorWebová`blazorwasm`sestava ( )
* BlazorServer`blazorserver`( )

Další informace Blazoro hostitelských modelech společnosti naleznete v tématu <xref:blazor/hosting-models>.

Podrobné pokyny k vytvoření Blazor aplikace ze šablony najdete <xref:blazor/get-started>v tématu .

## <a name="opno-locblazor-project-structure"></a>Blazorstruktura projektu

Následující soubory a složky Blazor tvoří aplikaci Blazor vygenerovanou ze šablony:

* *Program.cs* &ndash; Vstupní bod aplikace, který nastavuje:

  * ASP.NET [hlavní](xref:fundamentals/host/generic-host) Blazor hostitel (Server)
  * WebAssembly hostBlazor (WebAssembly) &ndash; Kód v tomto souboru Blazor je jedinečný`blazorwasm`pro aplikace vytvořené ze šablony WebAssembly ( ).
    * Komponenta, `App` která je kořenovou součástí aplikace, `app` je určena jako `Add` prvek MODELU DOM pro metodu.
    * Služby lze konfigurovat `ConfigureServices` s metodou na tvůrce `builder.Services.AddSingleton<IMyDependency, MyDependency>();`hostitele (například).
    * Konfiguraci lze dodat prostřednictvím`builder.Configuration`tvůrce hostitelů ( ).

* *Startup.cs* Blazor ( &ndash; Server) Obsahuje logiku spuštění aplikace. Třída `Startup` definuje dvě metody:

  * `ConfigureServices`&ndash; Konfiguruje [služby vkládání závislostí aplikace (DI).](xref:fundamentals/dependency-injection) V Blazor serverových aplikacích jsou <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>služby `WeatherForecastService` přidány voláním a je přidán `FetchData` do kontejneru služby pro použití ukázkovou komponentou.
  * `Configure`&ndash; Konfiguruje kanál pro zpracování požadavků aplikace:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>je volána k nastavení koncového bodu pro připojení v reálném čase s prohlížečem. Připojení je vytvořeno [SignalR](xref:signalr/introduction)s , což je rámec pro přidávání funkcí webu v reálném čase do aplikací.
    * [MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) se nazývá nastavení kořenové stránky aplikace (*Pages/_Host.cshtml)* a povolení navigace.

* *wwwroot/index.html* Blazor (WebAssembly) &ndash; Kořenová stránka aplikace implementovaná jako stránka HTML:
  * Pokud je nazvaná stránka aplikace původně požadována, tato stránka se vykreslí a vrátí v odpovědi.
  * Stránka určuje, kde `App` je kořenová komponenta vykreslena. `App` Komponenta (*App.razor*) `app` je určena jako `AddComponent` prvek `Startup.Configure`DOM k metodě v .
  * Soubor `_framework/blazor.webassembly.js` JavaScript je načten, který:
    * Stáhne soubor .NET runtime, aplikaci a závislosti aplikace.
    * Inicializuje runtime pro spuštění aplikace.

* *App.razor* &ndash; Kořenová součást aplikace, která nastavuje <xref:Microsoft.AspNetCore.Components.Routing.Router> směrování na straně klienta pomocí komponenty. Komponenta `Router` zachytí navigaci prohlížeče a vykreslí stránku, která odpovídá požadované adrese.

* *Složka* &ndash; Stránky Obsahuje směrovatelné komponenty/stránky (*.razor*), které tvoří Blazor aplikaci a kořenovou stránku Blazor Razor aplikace Server. Trasa pro každou stránku [`@page`](xref:mvc/views/razor#page) je určena pomocí směrnice. Šablona obsahuje následující:
  * *_Host.cshtml* Blazor (Server) &ndash; Kořenová stránka aplikace implementovaná jako Razor Page:
    * Pokud je nazvaná stránka aplikace původně požadována, tato stránka se vykreslí a vrátí v odpovědi.
    * Načte se `_framework/blazor.server.js` soubor JavaScriptu, který SignalR nastavuje spojení v reálném čase mezi prohlížečem a serverem.
    * Stránka Hostitele určuje, kde `App` je vykreslena kořenová komponenta (*App.razor).*
  * `Counter`(*Counter.razor*) &ndash; Implementuje stránku Čítač.
  * `Error`(*Error.razor*, Blazor Pouze serverová aplikace) &ndash; Vykresleno, když dojde k neošetřené výjimce v aplikaci.
  * `FetchData`(*FetchData.razor*) &ndash; Implementuje stránku Načíst data.
  * `Index`(*Index.razor*) &ndash; Implementuje domovskou stránku.

* *Sdílená* složka &ndash; Obsahuje další komponenty ui (*.razor),* které aplikace používá:
  * `MainLayout`(*MainLayout.razor*) &ndash; Součást rozložení aplikace.
  * `NavMenu`(*NavMenu.razor*) &ndash; Implementuje navigaci na postranním panelu. Zahrnuje [komponentu NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), která vykresluje navigační odkazy na jiné komponenty Razor. Komponenta `NavLink` automaticky indikuje vybraný stav při načítání její součásti, což uživateli pomáhá pochopit, která komponenta je aktuálně zobrazena.

* *_Imports.razor* &ndash; Zahrnuje běžné direktivy Razor, které mají být [`@using`](xref:mvc/views/razor#using) zahrnuty do komponent aplikace (*.razor*), jako jsou direktivy pro obory názvů.

* *Složka* datBlazor ( &ndash; Server) Obsahuje `WeatherForecast` třídu a `WeatherForecastService` implementaci, které poskytují `FetchData` ukázková data počasí pro komponentu aplikace.

* *wwwroot* &ndash; Složka [Kořenwebu](xref:fundamentals/index#web-root) pro aplikaci obsahující veřejné statické datové zdroje aplikace.

* *appsettings.json* Blazor ( &ndash; Server) Nastavení konfigurace aplikace.
