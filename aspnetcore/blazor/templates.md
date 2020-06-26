---
title: BlazorŠablony ASP.NET Core
author: guardrex
description: Přečtěte si o ASP.NET Core Blazor šablonách aplikací a Blazor struktuře projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 6359a02b23803f26c4a40772c68d39e804396403
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401893"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="31c7a-103">BlazorŠablony ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31c7a-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="31c7a-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="31c7a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="31c7a-105">BlazorRozhraní poskytuje šablony pro vývoj aplikací pro každý Blazor model hostování:</span><span class="sxs-lookup"><span data-stu-id="31c7a-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor WebAssembly<span data-ttu-id="31c7a-106"> (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="31c7a-106"> (`blazorwasm`)</span></span>
* Blazor Server<span data-ttu-id="31c7a-107"> (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="31c7a-107"> (`blazorserver`)</span></span>

<span data-ttu-id="31c7a-108">Další informace o Blazor modelech hostování naleznete v tématu <xref:blazor/hosting-models> .</span><span class="sxs-lookup"><span data-stu-id="31c7a-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="31c7a-109">Podrobné pokyny k vytvoření Blazor aplikace ze šablony naleznete v tématu <xref:blazor/get-started> .</span><span class="sxs-lookup"><span data-stu-id="31c7a-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="31c7a-110">Možnosti šablony jsou k dispozici předáním `--help` možnosti [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu CLI:</span><span class="sxs-lookup"><span data-stu-id="31c7a-110">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="31c7a-111">struktura projektu</span><span class="sxs-lookup"><span data-stu-id="31c7a-111"> project structure</span></span>

<span data-ttu-id="31c7a-112">Následující soubory a složky tvoří Blazor aplikaci vygenerovanou ze Blazor šablony:</span><span class="sxs-lookup"><span data-stu-id="31c7a-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="31c7a-113">`Program.cs`: Vstupní bod aplikace, který nastavuje:</span><span class="sxs-lookup"><span data-stu-id="31c7a-113">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="31c7a-114">[Hostitel](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )</span><span class="sxs-lookup"><span data-stu-id="31c7a-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="31c7a-115">Hostitel WebAssembly ( Blazor WebAssembly ): kód v tomto souboru je jedinečný pro aplikace vytvořené ze Blazor WebAssembly šablony ( `blazorwasm` ).</span><span class="sxs-lookup"><span data-stu-id="31c7a-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="31c7a-116">`App`Komponenta, která je kořenovou komponentou aplikace, je zadána jako `app` prvek modelu DOM pro `Add` metodu.</span><span class="sxs-lookup"><span data-stu-id="31c7a-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="31c7a-117">Služby lze konfigurovat pomocí `ConfigureServices` metody v Tvůrci hostitele (například `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).</span><span class="sxs-lookup"><span data-stu-id="31c7a-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="31c7a-118">Konfiguraci lze dodávat prostřednictvím tvůrce hostitele ( `builder.Configuration` ).</span><span class="sxs-lookup"><span data-stu-id="31c7a-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="31c7a-119">`Startup.cs`( Blazor Server ): Obsahuje logiku spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-119">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="31c7a-120">`Startup`Třída definuje dvě metody:</span><span class="sxs-lookup"><span data-stu-id="31c7a-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="31c7a-121">`ConfigureServices`: Konfiguruje služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="31c7a-122">V Blazor Server aplikacích se služby přidávají voláním <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> a přidá se `WeatherForecastService` do kontejneru služby pro použití v ukázkové `FetchData` součásti.</span><span class="sxs-lookup"><span data-stu-id="31c7a-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="31c7a-123">`Configure`: Konfiguruje kanál pro zpracování žádostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="31c7a-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="31c7a-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>je volána k nastavení koncového bodu pro připojení v reálném čase pomocí prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="31c7a-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="31c7a-125">Připojení se vytvoří pomocí [SignalR](xref:signalr/introduction) , což je rozhraní, které umožňuje přidávat do aplikací webové funkce v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="31c7a-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="31c7a-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*)se volá, aby se nastavila Kořenová stránka aplikace ( `Pages/_Host.cshtml` ) a povolila se navigace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="31c7a-127">`wwwroot/index.html`( Blazor WebAssembly ): Kořenová stránka aplikace implementovaná jako stránka HTML:</span><span class="sxs-lookup"><span data-stu-id="31c7a-127">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="31c7a-128">Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31c7a-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="31c7a-129">Stránka určuje, kde `App` je vygenerována kořenová komponenta.</span><span class="sxs-lookup"><span data-stu-id="31c7a-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="31c7a-130">`App`Součást ( `App.razor` ) je určena jako `app` prvek modelu DOM `AddComponent` metody v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="31c7a-130">The `App` component (`App.razor`) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="31c7a-131">`_framework/blazor.webassembly.js`Načte se soubor JavaScriptu, který:</span><span class="sxs-lookup"><span data-stu-id="31c7a-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="31c7a-132">Stáhne rozhraní .NET runtime, aplikaci a závislosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="31c7a-133">Inicializuje modul runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="31c7a-134">`App.razor`: Kořenová komponenta aplikace, která nastaví směrování na straně klienta pomocí <xref:Microsoft.AspNetCore.Components.Routing.Router> komponenty.</span><span class="sxs-lookup"><span data-stu-id="31c7a-134">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="31c7a-135"><xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta zachycuje navigaci v prohlížeči a vykreslí stránku, která odpovídá požadované adrese.</span><span class="sxs-lookup"><span data-stu-id="31c7a-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="31c7a-136">`Pages`Složka: obsahuje směrovatelný komponenty/stránky ( `.razor` ), které tvoří Blazor aplikaci a kořenovou Razor stránku Blazor Server aplikace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-136">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="31c7a-137">Trasa pro každou stránku je určena pomocí [`@page`](xref:mvc/views/razor#page) direktivy.</span><span class="sxs-lookup"><span data-stu-id="31c7a-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="31c7a-138">Šablona obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="31c7a-138">The template includes the following:</span></span>
  * <span data-ttu-id="31c7a-139">`_Host.cshtml`( Blazor Server ): Kořenová stránka aplikace byla implementována jako Razor Page</span><span class="sxs-lookup"><span data-stu-id="31c7a-139">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="31c7a-140">Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="31c7a-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="31c7a-141">`_framework/blazor.server.js`Načte se soubor JavaScriptu, který nastaví připojení v reálném čase SignalR mezi prohlížečem a serverem.</span><span class="sxs-lookup"><span data-stu-id="31c7a-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="31c7a-142">Stránka hostitel Určuje, kde `App` `App.razor` je vygenerována kořenová součást ().</span><span class="sxs-lookup"><span data-stu-id="31c7a-142">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="31c7a-143">`Counter`( `Pages/Counter.razor` ): Implementuje stránku čítače.</span><span class="sxs-lookup"><span data-stu-id="31c7a-143">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="31c7a-144">`Error`( `Error.razor` , Blazor Server jenom aplikace): vykreslí se, když v aplikaci dojde k neošetřené výjimce.</span><span class="sxs-lookup"><span data-stu-id="31c7a-144">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="31c7a-145">`FetchData`( `Pages/FetchData.razor` ): Implementuje stránku načíst data.</span><span class="sxs-lookup"><span data-stu-id="31c7a-145">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="31c7a-146">`Index`( `Pages/Index.razor` ): Implementuje domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="31c7a-146">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>

* <span data-ttu-id="31c7a-147">`Shared`Složka: obsahuje další součásti uživatelského rozhraní ( `.razor` ), které používá aplikace:</span><span class="sxs-lookup"><span data-stu-id="31c7a-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="31c7a-148">`MainLayout`( `MainLayout.razor` ): Součást rozložení aplikace</span><span class="sxs-lookup"><span data-stu-id="31c7a-148">`MainLayout` (`MainLayout.razor`): The app's layout component.</span></span>
  * <span data-ttu-id="31c7a-149">`NavMenu`( `NavMenu.razor` ): Implementuje navigaci bočním panelem.</span><span class="sxs-lookup"><span data-stu-id="31c7a-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="31c7a-150">Zahrnuje [ `NavLink` komponentu](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), která vykresluje navigační odkazy na jiné Razor součásti.</span><span class="sxs-lookup"><span data-stu-id="31c7a-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="31c7a-151"><xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta automaticky indikuje vybraný stav při načtení jeho komponenty, což pomáhá uživateli pochopit, která součást se aktuálně zobrazuje.</span><span class="sxs-lookup"><span data-stu-id="31c7a-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="31c7a-152">`_Imports.razor`: Obsahuje společné Razor direktivy, které se mají zahrnout do komponent aplikace (), jako jsou například `.razor` [`@using`](xref:mvc/views/razor#using) direktivy pro obory názvů.</span><span class="sxs-lookup"><span data-stu-id="31c7a-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="31c7a-153">`Data`Folder ( Blazor Server ): obsahuje `WeatherForecast` třídu a implementaci rozhraní `WeatherForecastService` , které poskytuje ukázková data o počasí `FetchData` součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="31c7a-154">`wwwroot`: [Kořenová složka webu](xref:fundamentals/index#web-root) pro aplikaci, která obsahuje veřejné statické prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="31c7a-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="31c7a-155">`appsettings.json`( Blazor Server ): Nastavení konfigurace pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="31c7a-155">`appsettings.json` (Blazor Server): Configuration settings for the app.</span></span>
