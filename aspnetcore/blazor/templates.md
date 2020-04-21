---
title: ASP.NET Blazor základní šablony
author: guardrex
description: Přečtěte si Blazor o ASP.NET Blazor šablon aplikací Core a struktuře projektů.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0a4a508beeae3d7bc665372d925989aa4e34ad52
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661718"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="a9434-103">ASP.NET Blazor základní šablony</span><span class="sxs-lookup"><span data-stu-id="a9434-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="a9434-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a9434-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a9434-105">Rámec Blazor poskytuje šablony pro vývoj aplikací Blazor pro každý z hostitelských modelů:</span><span class="sxs-lookup"><span data-stu-id="a9434-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="a9434-106">Webová`blazorwasm`sestava ( )</span><span class="sxs-lookup"><span data-stu-id="a9434-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="a9434-107">Server`blazorserver`( )</span><span class="sxs-lookup"><span data-stu-id="a9434-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="a9434-108">Další informace Blazoro hostitelských modelech společnosti naleznete v tématu <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="a9434-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="a9434-109">Podrobné pokyny k vytvoření Blazor aplikace ze šablony najdete <xref:blazor/get-started>v tématu .</span><span class="sxs-lookup"><span data-stu-id="a9434-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="a9434-110">Možnosti šablony jsou `--help` k dispozici předáním [možnosti dotnet nový](/dotnet/core/tools/dotnet-new) příkaz příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu příkazu:Template options are available by passing the option to the dotnet new CLI</span><span class="sxs-lookup"><span data-stu-id="a9434-110">Template options are available by passing the `--help` option to the [dotnet new](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="a9434-111">struktura projektu</span><span class="sxs-lookup"><span data-stu-id="a9434-111"> project structure</span></span>

<span data-ttu-id="a9434-112">Následující soubory a složky Blazor tvoří aplikaci Blazor vygenerovanou ze šablony:</span><span class="sxs-lookup"><span data-stu-id="a9434-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="a9434-113">*Program.cs* &ndash; Vstupní bod aplikace, který nastavuje:</span><span class="sxs-lookup"><span data-stu-id="a9434-113">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="a9434-114">ASP.NET [hlavní](xref:fundamentals/host/generic-host) Blazor hostitel (Server)</span><span class="sxs-lookup"><span data-stu-id="a9434-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="a9434-115">WebAssembly hostBlazor (WebAssembly) &ndash; Kód v tomto souboru Blazor je jedinečný`blazorwasm`pro aplikace vytvořené ze šablony WebAssembly ( ).</span><span class="sxs-lookup"><span data-stu-id="a9434-115">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="a9434-116">Komponenta, `App` která je kořenovou součástí aplikace, `app` je určena jako `Add` prvek MODELU DOM pro metodu.</span><span class="sxs-lookup"><span data-stu-id="a9434-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="a9434-117">Služby lze konfigurovat `ConfigureServices` s metodou na tvůrce `builder.Services.AddSingleton<IMyDependency, MyDependency>();`hostitele (například).</span><span class="sxs-lookup"><span data-stu-id="a9434-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="a9434-118">Konfiguraci lze dodat prostřednictvím`builder.Configuration`tvůrce hostitelů ( ).</span><span class="sxs-lookup"><span data-stu-id="a9434-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="a9434-119">*Startup.cs* Blazor ( &ndash; Server) Obsahuje logiku spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9434-119">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="a9434-120">Třída `Startup` definuje dvě metody:</span><span class="sxs-lookup"><span data-stu-id="a9434-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="a9434-121">`ConfigureServices`&ndash; Konfiguruje [služby vkládání závislostí aplikace (DI).](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="a9434-121">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="a9434-122">V Blazor serverových aplikacích jsou <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>služby `WeatherForecastService` přidány voláním a je přidán `FetchData` do kontejneru služby pro použití ukázkovou komponentou.</span><span class="sxs-lookup"><span data-stu-id="a9434-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="a9434-123">`Configure`&ndash; Konfiguruje kanál pro zpracování požadavků aplikace:</span><span class="sxs-lookup"><span data-stu-id="a9434-123">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="a9434-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>je volána k nastavení koncového bodu pro připojení v reálném čase s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="a9434-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="a9434-125">Připojení je vytvořeno [SignalR](xref:signalr/introduction)s , což je rámec pro přidávání funkcí webu v reálném čase do aplikací.</span><span class="sxs-lookup"><span data-stu-id="a9434-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="a9434-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) se nazývá nastavení kořenové stránky aplikace (*Pages/_Host.cshtml)* a povolení navigace.</span><span class="sxs-lookup"><span data-stu-id="a9434-126">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="a9434-127">*wwwroot/index.html* Blazor (WebAssembly) &ndash; Kořenová stránka aplikace implementovaná jako stránka HTML:</span><span class="sxs-lookup"><span data-stu-id="a9434-127">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="a9434-128">Pokud je nazvaná stránka aplikace původně požadována, tato stránka se vykreslí a vrátí v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a9434-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="a9434-129">Stránka určuje, kde `App` je kořenová komponenta vykreslena.</span><span class="sxs-lookup"><span data-stu-id="a9434-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="a9434-130">`App` Komponenta (*App.razor*) `app` je určena jako `AddComponent` prvek `Startup.Configure`DOM k metodě v .</span><span class="sxs-lookup"><span data-stu-id="a9434-130">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="a9434-131">Soubor `_framework/blazor.webassembly.js` JavaScript je načten, který:</span><span class="sxs-lookup"><span data-stu-id="a9434-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="a9434-132">Stáhne soubor .NET runtime, aplikaci a závislosti aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9434-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="a9434-133">Inicializuje runtime pro spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9434-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="a9434-134">*App.razor* &ndash; Kořenová součást aplikace, která nastavuje <xref:Microsoft.AspNetCore.Components.Routing.Router> směrování na straně klienta pomocí komponenty.</span><span class="sxs-lookup"><span data-stu-id="a9434-134">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="a9434-135">Komponenta `Router` zachytí navigaci prohlížeče a vykreslí stránku, která odpovídá požadované adrese.</span><span class="sxs-lookup"><span data-stu-id="a9434-135">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="a9434-136">*Složka* &ndash; Stránky Obsahuje směrovatelné komponenty/stránky (*.razor*), které tvoří Blazor aplikaci a kořenovou stránku Blazor Razor aplikace Server.</span><span class="sxs-lookup"><span data-stu-id="a9434-136">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="a9434-137">Trasa pro každou stránku [`@page`](xref:mvc/views/razor#page) je určena pomocí směrnice.</span><span class="sxs-lookup"><span data-stu-id="a9434-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a9434-138">Šablona obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="a9434-138">The template includes the following:</span></span>
  * <span data-ttu-id="a9434-139">*_Host.cshtml* Blazor (Server) &ndash; Kořenová stránka aplikace implementovaná jako Razor Page:</span><span class="sxs-lookup"><span data-stu-id="a9434-139">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="a9434-140">Pokud je nazvaná stránka aplikace původně požadována, tato stránka se vykreslí a vrátí v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a9434-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="a9434-141">Načte se `_framework/blazor.server.js` soubor JavaScriptu, který SignalR nastavuje spojení v reálném čase mezi prohlížečem a serverem.</span><span class="sxs-lookup"><span data-stu-id="a9434-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="a9434-142">Stránka Hostitele určuje, kde `App` je vykreslena kořenová komponenta (*App.razor).*</span><span class="sxs-lookup"><span data-stu-id="a9434-142">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="a9434-143">`Counter`(*Counter.razor*) &ndash; Implementuje stránku Čítač.</span><span class="sxs-lookup"><span data-stu-id="a9434-143">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="a9434-144">`Error`(*Error.razor*, Blazor Pouze serverová aplikace) &ndash; Vykresleno, když dojde k neošetřené výjimce v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a9434-144">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="a9434-145">`FetchData`(*FetchData.razor*) &ndash; Implementuje stránku Načíst data.</span><span class="sxs-lookup"><span data-stu-id="a9434-145">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="a9434-146">`Index`(*Index.razor*) &ndash; Implementuje domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="a9434-146">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="a9434-147">*Sdílená* složka &ndash; Obsahuje další komponenty ui (*.razor),* které aplikace používá:</span><span class="sxs-lookup"><span data-stu-id="a9434-147">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="a9434-148">`MainLayout`(*MainLayout.razor*) &ndash; Součást rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9434-148">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="a9434-149">`NavMenu`(*NavMenu.razor*) &ndash; Implementuje navigaci na postranním panelu.</span><span class="sxs-lookup"><span data-stu-id="a9434-149">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="a9434-150">Zahrnuje [komponentu NavLink](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), která vykresluje navigační odkazy na jiné komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="a9434-150">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="a9434-151">Komponenta `NavLink` automaticky indikuje vybraný stav při načítání její součásti, což uživateli pomáhá pochopit, která komponenta je aktuálně zobrazena.</span><span class="sxs-lookup"><span data-stu-id="a9434-151">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="a9434-152">*_Imports.razor* &ndash; Zahrnuje běžné direktivy Razor, které mají být [`@using`](xref:mvc/views/razor#using) zahrnuty do komponent aplikace (*.razor*), jako jsou direktivy pro obory názvů.</span><span class="sxs-lookup"><span data-stu-id="a9434-152">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="a9434-153">*Složka* datBlazor ( &ndash; Server) Obsahuje `WeatherForecast` třídu a `WeatherForecastService` implementaci, které poskytují `FetchData` ukázková data počasí pro komponentu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9434-153">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="a9434-154">*wwwroot* &ndash; Složka [Kořenwebu](xref:fundamentals/index#web-root) pro aplikaci obsahující veřejné statické datové zdroje aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9434-154">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="a9434-155">*appsettings.json* Blazor ( &ndash; Server) Nastavení konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9434-155">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
