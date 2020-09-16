---
title: Opožděné načtení sestavení v ASP.NET Core Blazor WebAssembly
author: guardrex
description: Zjistěte, jak se v aplikacích ASP.NET Core opožděně načítat sestavení Blazor WebAssembly .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: eb4aaa2f3d412cdf650ed2daf7c12166991d92a1
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592901"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="fe9d9-103">Opožděné načtení sestavení v ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fe9d9-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="fe9d9-104">Od [Safia Abdalla](https://safia.rocks) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe9d9-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fe9d9-105">Blazor WebAssembly výkon spuštění aplikace lze zlepšit odložením načítání některých sestavení aplikace, dokud nejsou požadovány, což se nazývá *opožděné načítání*.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="fe9d9-106">Například sestavení, která jsou použita pouze k vykreslení jedné součásti, lze nastavit tak, aby se načetla pouze v případě, že uživatel přejde k této součásti.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="fe9d9-107">Po načtení jsou sestavení uložená v mezipaměti na straně klienta a jsou dostupná pro všechny budoucí navigace.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="fe9d9-108">Blazorfunkce opožděného načítání umožňuje označit sestavení aplikace pro opožděné načítání, které načte sestavení během běhu, když uživatel přejde na konkrétní trasu.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="fe9d9-109">Tato funkce se skládá ze změn v souboru projektu a změn ve směrovači aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="fe9d9-110">Opožděné načítání sestavení nepřináší výhody Blazor Server aplikací, protože sestavení nejsou stažena do klienta v Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="fe9d9-111">Soubor projektu</span><span class="sxs-lookup"><span data-stu-id="fe9d9-111">Project file</span></span>

<span data-ttu-id="fe9d9-112">Označte sestavení pro opožděné načítání v souboru projektu aplikace ( `.csproj` ) pomocí `BlazorWebAssemblyLazyLoad` položky.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="fe9d9-113">Použijte název sestavení bez `.dll` přípony.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="fe9d9-114">BlazorRozhraní brání sestavení určenému touto skupinou položek v načtení při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="fe9d9-115">Následující příklad označuje velké vlastní sestavení ( `GrantImaharaRobotControls.dll` ) pro opožděné načtení.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="fe9d9-116">Pokud sestavení, které je označeno pro opožděné načítání, má závislosti, musí být také označeno pro opožděné načítání v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="fe9d9-117">`Router` (komponenta)</span><span class="sxs-lookup"><span data-stu-id="fe9d9-117">`Router` component</span></span>

<span data-ttu-id="fe9d9-118">Blazor`Router`Komponenta určuje, která sestavení Blazor vyhledává směrovatelné komponenty.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-118">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="fe9d9-119">`Router`Součást je také zodpovědná za vykreslování součásti pro trasu, ve které uživatel naviguje.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-119">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="fe9d9-120">`Router`Komponenta podporuje `OnNavigateAsync` funkci, kterou lze použít ve spojení s opožděným načítáním.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-120">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="fe9d9-121">V `Router` součásti aplikace ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="fe9d9-121">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="fe9d9-122">Přidejte `OnNavigateAsync` zpětné volání.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-122">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="fe9d9-123">`OnNavigateAsync`Obslužná rutina se vyvolá v případě, že uživatel:</span><span class="sxs-lookup"><span data-stu-id="fe9d9-123">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="fe9d9-124">Poprvé navštíví trasu, a to tak, že přejde přímo z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-124">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="fe9d9-125">Přejde na novou trasu pomocí odkazu nebo <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> vyvolání.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-125">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="fe9d9-126">Pokud sestavení s opožděným zatížením obsahují směrovatelné komponenty, přidejte do komponenty [seznam](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (například s názvem `lazyLoadedAssemblies` ).</span><span class="sxs-lookup"><span data-stu-id="fe9d9-126">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="fe9d9-127">Sestavení jsou předána zpět do <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> kolekce v případě, že sestavení obsahují směrovatelný komponenty.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-127">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="fe9d9-128">Architektura hledá sestavení pro trasy a aktualizuje kolekci tras, pokud jsou nalezeny jakékoli nové trasy.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-128">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="fe9d9-129">Pokud `OnNavigateAsync` zpětné volání vyvolá neošetřenou výjimku, je vyvolána [ Blazor Chyba uživatelského rozhraní](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) .</span><span class="sxs-lookup"><span data-stu-id="fe9d9-129">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="fe9d9-130">Logika načtení sestavení v `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="fe9d9-130">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="fe9d9-131">`OnNavigateAsync` má `NavigationContext` parametr, který poskytuje informace o aktuální asynchronní události navigace, včetně cílové cesty ( `Path` ) a tokenu zrušení ( `CancellationToken` ):</span><span class="sxs-lookup"><span data-stu-id="fe9d9-131">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="fe9d9-132">`Path`Vlastnost je cílová cesta uživatele relativní vzhledem k základní cestě aplikace, jako je například `/robot` .</span><span class="sxs-lookup"><span data-stu-id="fe9d9-132">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="fe9d9-133">`CancellationToken`Lze použít ke sledování zrušení asynchronní úlohy.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-133">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="fe9d9-134">`OnNavigateAsync` automaticky zruší aktuálně běžící navigační úkol, když uživatel přejde na jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-134">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="fe9d9-135">Uvnitř `OnNavigateAsync` , implementujte logiku pro určení sestavení, která se mají načíst.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-135">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="fe9d9-136">Vaše možnosti jsou:</span><span class="sxs-lookup"><span data-stu-id="fe9d9-136">Options include:</span></span>

* <span data-ttu-id="fe9d9-137">Podmíněné kontroly v rámci `OnNavigateAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-137">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="fe9d9-138">Vyhledávací tabulka, která mapuje trasy k názvům sestavení, vložena do komponenty nebo implementována v rámci [`@code`](xref:mvc/views/razor#code) bloku.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-138">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="fe9d9-139">`LazyAssemblyLoader` je služba typu Singleton poskytovaná rozhraním pro načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-139">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="fe9d9-140">Vložit `LazyAssemblyLoader` do `Router` komponenty:</span><span class="sxs-lookup"><span data-stu-id="fe9d9-140">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="fe9d9-141">`LazyAssemblyLoader`Poskytuje `LoadAssembliesAsync` metodu, která:</span><span class="sxs-lookup"><span data-stu-id="fe9d9-141">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="fe9d9-142">Používá zprostředkovatele komunikace JS pro načítání sestavení prostřednictvím síťového volání.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-142">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="fe9d9-143">Načte sestavení do modulu runtime spuštěného na webovém sestavení v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-143">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="fe9d9-144">Implementace opožděného načítání rozhraní podporuje opožděné načítání s předvykreslováním v hostovaném Blazor řešení.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-144">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted Blazor solution.</span></span> <span data-ttu-id="fe9d9-145">Při předvykreslování se předpokládá, že budou načtena všechna sestavení, včetně těch, která jsou označena pro opožděné načítání.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-145">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="fe9d9-146">Ruční registrace `LazyAssemblyLoader` v metodě *serverového* projektu `Startup.ConfigureServices` ( `Startup.cs` ):</span><span class="sxs-lookup"><span data-stu-id="fe9d9-146">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddSingleton<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="fe9d9-147">Interakce uživatele s `<Navigating>` obsahem</span><span class="sxs-lookup"><span data-stu-id="fe9d9-147">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="fe9d9-148">Při načítání sestavení, která mohou trvat několik sekund, `Router` může komponenta uživateli indikovat, že dojde k přechodu na stránku:</span><span class="sxs-lookup"><span data-stu-id="fe9d9-148">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="fe9d9-149">Přidejte [`@using`](xref:mvc/views/razor#using) direktivu pro <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-149">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="fe9d9-150">Přidejte `<Navigating>` značku do komponenty se značkami, které se mají zobrazit během událostí přechodu stránky.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-150">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="fe9d9-151">Zpracovat zrušení v `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="fe9d9-151">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="fe9d9-152">`NavigationContext`Objekt předaný `OnNavigateAsync` zpětnému volání obsahuje `CancellationToken` sadu, která je nastavena, když dojde k nové události navigace.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-152">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="fe9d9-153">`OnNavigateAsync`Zpětné volání musí vyvolat, když je tento token zrušení nastaven tak, aby se nepokračovalo `OnNavigateAsync` v provádění zpětného volání u zastaralé navigace.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-153">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="fe9d9-154">Pokud uživatel přejde na trasu a a okamžitě ke směrování B, aplikace by neměla pokračovat ve spouštění `OnNavigateAsync` zpětného volání pro trasu a:</span><span class="sxs-lookup"><span data-stu-id="fe9d9-154">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="fe9d9-155">Není aktivována, pokud je zrušený token zrušení v rámci zrušení `NavigationContext` zamýšleného chování, například vykreslování komponenty z předchozí navigace.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-155">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="fe9d9-156">`OnNavigateAsync` události a přejmenované soubory sestavení</span><span class="sxs-lookup"><span data-stu-id="fe9d9-156">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="fe9d9-157">Zavaděč prostředků spoléhá na názvy sestavení, které jsou definovány v `blazor.boot.json` souboru.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-157">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="fe9d9-158">Pokud [jsou sestavení přejmenována](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), názvy sestavení používané v `OnNavigateAsync` metodách a názvy sestavení v `blazor.boot.json` souboru nejsou synchronizovány.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-158">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="fe9d9-159">K nápravě:</span><span class="sxs-lookup"><span data-stu-id="fe9d9-159">To rectify this:</span></span>

* <span data-ttu-id="fe9d9-160">Zkontrolujte, jestli je aplikace spuštěná v produkčním prostředí při určování názvů sestavení, která se mají použít.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-160">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="fe9d9-161">Uložte názvy přejmenovaných sestavení do samostatného souboru a načtěte ho z tohoto souboru a určete, který název sestavení se má použít v `LazyLoadAssemblyService` `OnNavigateAsync` metodách a.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-161">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="fe9d9-162">Kompletní příklad</span><span class="sxs-lookup"><span data-stu-id="fe9d9-162">Complete example</span></span>

<span data-ttu-id="fe9d9-163">Následující kompletní `Router` Komponenta ukazuje načtení sestavení, `GrantImaharaRobotControls.dll` když uživatel přejde na `/robot` .</span><span class="sxs-lookup"><span data-stu-id="fe9d9-163">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="fe9d9-164">Během přechodů stránky se uživateli zobrazí zpráva s uvedením stylu.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-164">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="fe9d9-165">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="fe9d9-165">Troubleshoot</span></span>

* <span data-ttu-id="fe9d9-166">Pokud dojde k neočekávanému vykreslení (například je vykreslena komponenta z předchozí navigace), zkontrolujte, zda kód vyvolá výjimku, pokud je nastaven token zrušení.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-166">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="fe9d9-167">Pokud jsou sestavení stále načtena při spuštění aplikace, ověřte, zda je sestavení označeno jako opožděně načteno v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="fe9d9-167">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe9d9-168">Další materiály</span><span class="sxs-lookup"><span data-stu-id="fe9d9-168">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
