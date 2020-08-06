---
title: BlazorŠablony ASP.NET Core
author: guardrex
description: Přečtěte si o ASP.NET Core Blazor šablonách aplikací a Blazor struktuře projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 65d6a3156419b57eae6c7e41a9778fa25fd88f4f
ms.sourcegitcommit: 6eacadf1be61679ab8e6f781ece76b7395512879
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758525"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="651ac-103">BlazorŠablony ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="651ac-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="651ac-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="651ac-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="651ac-105">BlazorRozhraní poskytuje šablony pro vývoj aplikací pro každý Blazor model hostování:</span><span class="sxs-lookup"><span data-stu-id="651ac-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="651ac-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="651ac-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="651ac-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="651ac-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="651ac-108">Další informace o Blazor modelech hostování naleznete v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="651ac-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="651ac-109">Možnosti šablony jsou k dispozici předáním `--help` možnosti [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu CLI:</span><span class="sxs-lookup"><span data-stu-id="651ac-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="651ac-110">Blazorstruktura projektu</span><span class="sxs-lookup"><span data-stu-id="651ac-110">Blazor project structure</span></span>

<span data-ttu-id="651ac-111">Následující soubory a složky tvoří Blazor aplikaci vygenerovanou ze Blazor šablony projektu:</span><span class="sxs-lookup"><span data-stu-id="651ac-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

* <span data-ttu-id="651ac-112">`Program.cs`: Vstupní bod aplikace, který nastavuje:</span><span class="sxs-lookup"><span data-stu-id="651ac-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="651ac-113">[Hostitel](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="651ac-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="651ac-114">Hostitel WebAssembly ( Blazor WebAssembly ): kód v tomto souboru je jedinečný pro aplikace vytvořené ze Blazor WebAssembly šablony ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="651ac-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="651ac-115">`App`Součást je kořenová komponenta aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="651ac-116">`App`Komponenta je určena jako `app` element modelu DOM ( `<app>...</app>` ) pro kolekci kořenových komponent ( `builder.RootComponents.Add<App>("app")` ).</span><span class="sxs-lookup"><span data-stu-id="651ac-116">The `App` component is specified as the `app` DOM element (`<app>...</app>`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="651ac-117">[Služby](xref:blazor/fundamentals/dependency-injection) jsou přidány a nakonfigurovány (například `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).</span><span class="sxs-lookup"><span data-stu-id="651ac-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

* <span data-ttu-id="651ac-118">`Startup.cs`( Blazor Server ): Obsahuje logiku spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="651ac-119">`Startup`Třída definuje dvě metody:</span><span class="sxs-lookup"><span data-stu-id="651ac-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="651ac-120">`ConfigureServices`: Konfiguruje služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="651ac-121">V Blazor Server aplikacích se služby přidávají voláním <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> a přidá se `WeatherForecastService` do kontejneru služby pro použití v ukázkové `FetchData` součásti.</span><span class="sxs-lookup"><span data-stu-id="651ac-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="651ac-122">`Configure`: Konfiguruje kanál pro zpracování žádostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="651ac-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="651ac-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>je volána k nastavení koncového bodu pro připojení v reálném čase pomocí prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="651ac-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="651ac-124">Připojení se vytvoří pomocí [SignalR](xref:signalr/introduction) , což je rozhraní, které umožňuje přidávat do aplikací webové funkce v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="651ac-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="651ac-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)se volá, aby se nastavila Kořenová stránka aplikace ( `Pages/_Host.cshtml` ) a povolila se navigace.</span><span class="sxs-lookup"><span data-stu-id="651ac-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="651ac-126">`wwwroot/index.html`( Blazor WebAssembly ): Kořenová stránka aplikace implementovaná jako stránka HTML:</span><span class="sxs-lookup"><span data-stu-id="651ac-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="651ac-127">Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="651ac-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="651ac-128">Stránka určuje, kde `App` je vygenerována kořenová komponenta.</span><span class="sxs-lookup"><span data-stu-id="651ac-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="651ac-129">Komponenta je vykreslena v umístění `app` elementu modelu DOM ( `<app>...</app>` ).</span><span class="sxs-lookup"><span data-stu-id="651ac-129">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="651ac-130">`_framework/blazor.webassembly.js`Načte se soubor JavaScriptu, který:</span><span class="sxs-lookup"><span data-stu-id="651ac-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="651ac-131">Stáhne rozhraní .NET runtime, aplikaci a závislosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="651ac-132">Inicializuje modul runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="651ac-133">`App.razor`: Kořenová komponenta aplikace, která nastaví směrování na straně klienta pomocí <xref:Microsoft.AspNetCore.Components.Routing.Router> komponenty.</span><span class="sxs-lookup"><span data-stu-id="651ac-133">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="651ac-134"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta zachycuje navigaci v prohlížeči a vykreslí stránku, která odpovídá požadované adrese.</span><span class="sxs-lookup"><span data-stu-id="651ac-134">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="651ac-135">`Pages`Složka: obsahuje směrovatelný komponenty/stránky ( `.razor` ), které tvoří Blazor aplikaci a kořenovou Razor stránku Blazor Server aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-135">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="651ac-136">Trasa pro každou stránku je určena pomocí [`@page`](xref:mvc/views/razor#page) direktivy.</span><span class="sxs-lookup"><span data-stu-id="651ac-136">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="651ac-137">Šablona obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="651ac-137">The template includes the following:</span></span>
  * <span data-ttu-id="651ac-138">`_Host.cshtml`( Blazor Server ): Kořenová stránka aplikace byla implementována jako Razor Page</span><span class="sxs-lookup"><span data-stu-id="651ac-138">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="651ac-139">Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="651ac-139">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="651ac-140">`_framework/blazor.server.js`Načte se soubor JavaScriptu, který nastaví připojení v reálném čase SignalR mezi prohlížečem a serverem.</span><span class="sxs-lookup"><span data-stu-id="651ac-140">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="651ac-141">Stránka hostitel Určuje, kde `App` `App.razor` je vygenerována kořenová součást ().</span><span class="sxs-lookup"><span data-stu-id="651ac-141">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="651ac-142">`Counter`( `Pages/Counter.razor` ): Implementuje stránku čítače.</span><span class="sxs-lookup"><span data-stu-id="651ac-142">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="651ac-143">`Error`( `Error.razor` , Blazor Server jenom aplikace): vykreslí se, když v aplikaci dojde k neošetřené výjimce.</span><span class="sxs-lookup"><span data-stu-id="651ac-143">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="651ac-144">`FetchData`( `Pages/FetchData.razor` ): Implementuje stránku načíst data.</span><span class="sxs-lookup"><span data-stu-id="651ac-144">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="651ac-145">`Index`( `Pages/Index.razor` ): Implementuje domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="651ac-145">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="651ac-146">`Properties/launchSettings.json`: Obsahuje [konfiguraci vývojového prostředí](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="651ac-146">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

* <span data-ttu-id="651ac-147">`Shared`Složka: obsahuje další součásti uživatelského rozhraní ( `.razor` ), které používá aplikace:</span><span class="sxs-lookup"><span data-stu-id="651ac-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="651ac-148">`MainLayout`( `MainLayout.razor` ): [Součást rozložení](xref:blazor/layouts)aplikace</span><span class="sxs-lookup"><span data-stu-id="651ac-148">`MainLayout` (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="651ac-149">`NavMenu`( `NavMenu.razor` ): Implementuje navigaci bočním panelem.</span><span class="sxs-lookup"><span data-stu-id="651ac-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="651ac-150">Zahrnuje [ `NavLink` komponentu](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), která vykresluje navigační odkazy na jiné Razor součásti.</span><span class="sxs-lookup"><span data-stu-id="651ac-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="651ac-151"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta automaticky indikuje vybraný stav při načtení jeho komponenty, což pomáhá uživateli pochopit, která součást se aktuálně zobrazuje.</span><span class="sxs-lookup"><span data-stu-id="651ac-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="651ac-152">`_Imports.razor`: Obsahuje společné Razor direktivy, které se mají zahrnout do komponent aplikace (), jako jsou například `.razor` [`@using`](xref:mvc/views/razor#using) direktivy pro obory názvů.</span><span class="sxs-lookup"><span data-stu-id="651ac-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="651ac-153">`Data`Folder ( Blazor Server ): obsahuje `WeatherForecast` třídu a implementaci rozhraní `WeatherForecastService` , které poskytuje ukázková data o počasí `FetchData` součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="651ac-154">`wwwroot`: [Kořenová složka webu](xref:fundamentals/index#web-root) pro aplikaci, která obsahuje veřejné statické prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="651ac-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="651ac-155">`appsettings.json`: Uchovává [nastavení konfigurace](xref:blazor/fundamentals/configuration) pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="651ac-155">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="651ac-156">V Blazor WebAssembly aplikaci je soubor nastavení aplikace umístěný ve `wwwroot` složce.</span><span class="sxs-lookup"><span data-stu-id="651ac-156">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="651ac-157">V Blazor Server aplikaci je soubor nastavení aplikace umístěný v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="651ac-157">In a Blazor Server app, the app settings file is located at the project root.</span></span>
