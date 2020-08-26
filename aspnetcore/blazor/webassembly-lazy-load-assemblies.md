---
title: Opožděné načtení sestavení v ASP.NET Core Blazor WebAssembly
author: guardrex
description: Zjistěte, jak se v aplikacích ASP.NET Core opožděně načítat sestavení Blazor WebAssembly .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
ms.openlocfilehash: 46f98080ad40f614f9cb1af2190f263d205c1016
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865161"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="7ec1d-103">Opožděné načtení sestavení v ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7ec1d-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="7ec1d-104">Od [Safia Abdalla](https://safia.rocks) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7ec1d-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ec1d-105">Blazor WebAssembly výkon spuštění aplikace lze zlepšit odložením načítání některých sestavení aplikace, dokud nejsou požadovány, což se nazývá *opožděné načítání*.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="7ec1d-106">Například sestavení, která jsou použita pouze k vykreslení jedné součásti, lze nastavit tak, aby se načetla pouze v případě, že uživatel přejde k této součásti.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="7ec1d-107">Po načtení jsou sestavení uložená v mezipaměti na straně klienta a jsou dostupná pro všechny budoucí navigace.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="7ec1d-108">Blazorfunkce opožděného načítání umožňuje označit sestavení aplikace pro opožděné načítání, které načte sestavení během běhu, když uživatel přejde na konkrétní trasu.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="7ec1d-109">Tato funkce se skládá ze změn v souboru projektu a změn ve směrovači aplikace.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="7ec1d-110">Opožděné načítání sestavení nepřináší výhody Blazor Server aplikací, protože sestavení nejsou stažena do klienta v Blazor Server aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="7ec1d-111">Soubor projektu</span><span class="sxs-lookup"><span data-stu-id="7ec1d-111">Project file</span></span>

<span data-ttu-id="7ec1d-112">Označte sestavení pro opožděné načítání v souboru projektu aplikace ( `.csproj` ) pomocí `BlazorWebAssemblyLazyLoad` položky.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="7ec1d-113">Použijte název sestavení bez `.dll` přípony.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="7ec1d-114">BlazorRozhraní brání sestavení určenému touto skupinou položek v načtení při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="7ec1d-115">Následující příklad označuje velké vlastní sestavení ( `GrantImaharaRobotControls.dll` ) pro opožděné načtení.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="7ec1d-116">Pokud sestavení, které je označeno pro opožděné načítání, má závislosti, musí být také označeno pro opožděné načítání v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="7ec1d-117">Laxně vytvářená načíst lze pouze sestavení, která používá aplikace.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="7ec1d-118">Linker nepoužívá sestavení z publikovaného výstupu.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-118">The linker strips unused assemblies from published output.</span></span>

> [!NOTE]
> <span data-ttu-id="7ec1d-119">V rozhraní .NET 5 Release Candidate 1 (RC1) nebo novějším, které bude vydáno v polovině září, bude název sestavení vyžadovat `.dll` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-119">In .NET 5 Release Candidate 1 (RC1) or later, which will be released in mid-September, the assembly name will require the `.dll` extension:</span></span>
>
> ```xml
> <ItemGroup>
>  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
> </ItemGroup>
> ```

## <a name="router-component"></a><span data-ttu-id="7ec1d-120">`Router` (komponenta)</span><span class="sxs-lookup"><span data-stu-id="7ec1d-120">`Router` component</span></span>

<span data-ttu-id="7ec1d-121">Blazor`Router`Komponenta určuje, která sestavení Blazor vyhledává směrovatelné komponenty.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-121">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="7ec1d-122">`Router`Součást je také zodpovědná za vykreslování součásti pro trasu, ve které uživatel naviguje.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-122">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="7ec1d-123">`Router`Komponenta podporuje `OnNavigateAsync` funkci, kterou lze použít ve spojení s opožděným načítáním.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-123">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="7ec1d-124">V `Router` součásti aplikace ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="7ec1d-124">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="7ec1d-125">Přidejte `OnNavigateAsync` zpětné volání.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-125">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="7ec1d-126">`OnNavigateAsync`Obslužná rutina se vyvolá v případě, že uživatel:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-126">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="7ec1d-127">Poprvé navštíví trasu, a to tak, že přejde přímo z prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-127">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="7ec1d-128">Přejde na novou trasu pomocí odkazu nebo <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> vyvolání.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-128">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="7ec1d-129">Pokud sestavení s opožděným zatížením obsahují směrovatelné komponenty, přidejte do komponenty [seznam](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (například s názvem `lazyLoadedAssemblies` ).</span><span class="sxs-lookup"><span data-stu-id="7ec1d-129">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="7ec1d-130">Sestavení jsou předána zpět do <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> kolekce v případě, že sestavení obsahují směrovatelný komponenty.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-130">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="7ec1d-131">Architektura hledá sestavení pro trasy a aktualizuje kolekci tras, pokud jsou nalezeny jakékoli nové trasy.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-131">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

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

<span data-ttu-id="7ec1d-132">Pokud `OnNavigateAsync` zpětné volání vyvolá neošetřenou výjimku, je vyvolána [ Blazor Chyba uživatelského rozhraní](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) .</span><span class="sxs-lookup"><span data-stu-id="7ec1d-132">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="7ec1d-133">Logika načtení sestavení v `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="7ec1d-133">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="7ec1d-134">`OnNavigateAsync` má `NavigationContext` parametr, který poskytuje informace o aktuální asynchronní události navigace, včetně cílové cesty ( `Path` ) a tokenu zrušení ( `CancellationToken` ):</span><span class="sxs-lookup"><span data-stu-id="7ec1d-134">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="7ec1d-135">`Path`Vlastnost je cílová cesta uživatele relativní vzhledem k základní cestě aplikace, jako je například `/robot` .</span><span class="sxs-lookup"><span data-stu-id="7ec1d-135">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="7ec1d-136">`CancellationToken`Lze použít ke sledování zrušení asynchronní úlohy.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-136">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="7ec1d-137">`OnNavigateAsync` automaticky zruší aktuálně běžící navigační úkol, když uživatel přejde na jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-137">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="7ec1d-138">Uvnitř `OnNavigateAsync` , implementujte logiku pro určení sestavení, která se mají načíst.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-138">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="7ec1d-139">Vaše možnosti jsou:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-139">Options include:</span></span>

* <span data-ttu-id="7ec1d-140">Podmíněné kontroly v rámci `OnNavigateAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-140">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="7ec1d-141">Vyhledávací tabulka, která mapuje trasy k názvům sestavení, vložena do komponenty nebo implementována v rámci [`@code`](xref:mvc/views/razor#code) bloku.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-141">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="7ec1d-142">`LazyAssemblyLoader` je služba typu Singleton poskytovaná rozhraním pro načítání sestavení.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-142">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="7ec1d-143">Vložit `LazyAssemblyLoader` do `Router` komponenty:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-143">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="7ec1d-144">`LazyAssemblyLoader`Poskytuje `LoadAssembliesAsync` metodu, která:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-144">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="7ec1d-145">Používá zprostředkovatele komunikace JS pro načítání sestavení prostřednictvím síťového volání.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-145">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="7ec1d-146">Načte sestavení do modulu runtime spuštěného na webovém sestavení v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-146">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="7ec1d-147">Implementace opožděného načítání rozhraní podporuje předvykreslování na serveru.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-147">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="7ec1d-148">Při předvykreslování se předpokládá, že budou načtena všechna sestavení, včetně těch, která jsou označena pro opožděné načítání.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-148">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="7ec1d-149">Interakce uživatele s `<Navigating>` obsahem</span><span class="sxs-lookup"><span data-stu-id="7ec1d-149">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="7ec1d-150">Při načítání sestavení, která mohou trvat několik sekund, `Router` může komponenta uživateli indikovat, že dojde k přechodu na stránku:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-150">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="7ec1d-151">Přidejte [`@using`](xref:mvc/views/razor#using) direktivu pro <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> obor názvů.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-151">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="7ec1d-152">Přidejte `<Navigating>` značku do komponenty se značkami, které se mají zobrazit během událostí přechodu stránky.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-152">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

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

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="7ec1d-153">Zpracovat zrušení v `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="7ec1d-153">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="7ec1d-154">`NavigationContext`Objekt předaný `OnNavigateAsync` zpětnému volání obsahuje `CancellationToken` sadu, která je nastavena, když dojde k nové události navigace.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-154">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="7ec1d-155">`OnNavigateAsync`Zpětné volání musí vyvolat, když je tento token zrušení nastaven tak, aby se nepokračovalo `OnNavigateAsync` v provádění zpětného volání u zastaralé navigace.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-155">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="7ec1d-156">Pokud uživatel přejde na trasu a a okamžitě ke směrování B, aplikace by neměla pokračovat ve spouštění `OnNavigateAsync` zpětného volání pro trasu a:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-156">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

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
> <span data-ttu-id="7ec1d-157">Není aktivována, pokud je zrušený token zrušení v rámci zrušení `NavigationContext` zamýšleného chování, například vykreslování komponenty z předchozí navigace.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-157">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="7ec1d-158">`OnNavigateAsync` události a přejmenované soubory sestavení</span><span class="sxs-lookup"><span data-stu-id="7ec1d-158">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="7ec1d-159">Zavaděč prostředků spoléhá na názvy sestavení, které jsou definovány v `blazor.boot.json` souboru.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-159">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="7ec1d-160">Pokud [jsou sestavení přejmenována](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), názvy sestavení používané v `OnNavigateAsync` metodách a názvy sestavení v `blazor.boot.json` souboru nejsou synchronizovány.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-160">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="7ec1d-161">K nápravě:</span><span class="sxs-lookup"><span data-stu-id="7ec1d-161">To rectify this:</span></span>

* <span data-ttu-id="7ec1d-162">Zkontrolujte, jestli je aplikace spuštěná v produkčním prostředí při určování názvů sestavení, která se mají použít.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-162">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="7ec1d-163">Uložte názvy přejmenovaných sestavení do samostatného souboru a načtěte ho z tohoto souboru a určete, který název sestavení se má použít v `LazyLoadAssemblyService` `OnNavigateAsync` metodách a.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-163">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="7ec1d-164">Kompletní příklad</span><span class="sxs-lookup"><span data-stu-id="7ec1d-164">Complete example</span></span>

<span data-ttu-id="7ec1d-165">Následující kompletní `Router` Komponenta ukazuje načtení sestavení, `GrantImaharaRobotControls.dll` když uživatel přejde na `/robot` .</span><span class="sxs-lookup"><span data-stu-id="7ec1d-165">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="7ec1d-166">Během přechodů stránky se uživateli zobrazí zpráva s uvedením stylu.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-166">During page transitions, a styled message is displayed to the user.</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="7ec1d-167">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="7ec1d-167">Troubleshoot</span></span>

* <span data-ttu-id="7ec1d-168">Pokud dojde k neočekávanému vykreslení (například je vykreslena komponenta z předchozí navigace), zkontrolujte, zda kód vyvolá výjimku, pokud je nastaven token zrušení.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-168">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="7ec1d-169">Pokud jsou sestavení stále načtena při spuštění aplikace, ověřte, zda je sestavení označeno jako opožděně načteno v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7ec1d-169">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ec1d-170">Další materiály</span><span class="sxs-lookup"><span data-stu-id="7ec1d-170">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
