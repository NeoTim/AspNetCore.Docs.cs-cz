---
title: Opožděné načtení sestavení v ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Zjistěte, jak se v aplikacích ASP.NET Core opožděně načítat sestavení Blazor WebAssembly .
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 0fb744b4e9d44e6b8136123fddfb75ace8901d52
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819940"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>Opožděné načtení sestavení v ASP.NET CoreBlazor WebAssembly

Od [Safia Abdalla](https://safia.rocks) a [Luke Latham](https://github.com/guardrex)

Blazor WebAssemblyvýkon spuštění aplikace lze zlepšit odložením načítání některých sestavení aplikace, dokud nejsou požadovány, což se nazývá *opožděné načítání*. Například sestavení, která jsou použita pouze k vykreslení jedné součásti, lze nastavit tak, aby se načetla pouze v případě, že uživatel přejde k této součásti. Po načtení jsou sestavení uložená v mezipaměti na straně klienta a jsou dostupná pro všechny budoucí navigace.

Blazorfunkce opožděného načítání umožňuje označit sestavení aplikace pro opožděné načítání, které načte sestavení během běhu, když uživatel přejde na konkrétní trasu. Tato funkce se skládá ze změn v souboru projektu a změn ve směrovači aplikace.

> [!NOTE]
> Opožděné načítání sestavení nepřináší výhody Blazor Server aplikací, protože sestavení nejsou stažena do klienta v Blazor Server aplikaci.

## <a name="project-file"></a>Soubor projektu

Označte sestavení pro opožděné načítání v souboru projektu aplikace ( `.csproj` ) pomocí `BlazorWebAssemblyLazyLoad` položky. Použijte název sestavení bez `.dll` přípony. BlazorRozhraní brání sestavení určenému touto skupinou položek v načtení při spuštění aplikace. Následující příklad označuje velké vlastní sestavení ( `GrantImaharaRobotControls.dll` ) pro opožděné načtení. Pokud sestavení, které je označeno pro opožděné načítání, má závislosti, musí být také označeno pro opožděné načítání v souboru projektu.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

Laxně vytvářená načíst lze pouze sestavení, která používá aplikace. Linker nepoužívá sestavení z publikovaného výstupu.

## <a name="router-component"></a>`Router` (komponenta)

Blazor`Router`Komponenta určuje, která sestavení Blazor vyhledává směrovatelné komponenty. `Router`Součást je také zodpovědná za vykreslování součásti pro trasu, ve které uživatel naviguje. `Router`Komponenta podporuje `OnNavigateAsync` funkci, kterou lze použít ve spojení s opožděným načítáním.

V `Router` součásti aplikace ( `App.razor` ):

* Přidejte `OnNavigateAsync` zpětné volání. `OnNavigateAsync`Obslužná rutina se vyvolá v případě, že uživatel:
  * Poprvé navštíví trasu, a to tak, že přejde přímo z prohlížeče.
  * Přejde na novou trasu pomocí odkazu nebo <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> vyvolání.
* Pokud sestavení s opožděným zatížením obsahují směrovatelné komponenty, přidejte do komponenty [seznam](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (například s názvem `lazyLoadedAssemblies` ). Sestavení jsou předána zpět do <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> kolekce v případě, že sestavení obsahují směrovatelný komponenty. Architektura hledá sestavení pro trasy a aktualizuje kolekci tras, pokud jsou nalezeny jakékoli nové trasy.

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

Pokud `OnNavigateAsync` zpětné volání vyvolá neošetřenou výjimku, je vyvolána [ Blazor Chyba uživatelského rozhraní](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) .

### <a name="assembly-load-logic-in-onnavigateasync"></a>Logika načtení sestavení v`OnNavigateAsync`

`OnNavigateAsync`má `NavigationContext` parametr, který poskytuje informace o aktuální asynchronní události navigace, včetně cílové cesty ( `Path` ) a tokenu zrušení ( `CancellationToken` ):

* `Path`Vlastnost je cílová cesta uživatele relativní vzhledem k základní cestě aplikace, jako je například `/robot` .
* `CancellationToken`Lze použít ke sledování zrušení asynchronní úlohy. `OnNavigateAsync`automaticky zruší aktuálně běžící navigační úkol, když uživatel přejde na jinou stránku.

Uvnitř `OnNavigateAsync` , implementujte logiku pro určení sestavení, která se mají načíst. Vaše možnosti jsou:

* Podmíněné kontroly v rámci `OnNavigateAsync` metody.
* Vyhledávací tabulka, která mapuje trasy k názvům sestavení, vložena do komponenty nebo implementována v rámci [`@code`](xref:mvc/views/razor#code) bloku.

`LazyAssemblyLoader`je služba typu Singleton poskytovaná rozhraním pro načítání sestavení. Vložit `LazyAssemblyLoader` do `Router` komponenty:

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader`Poskytuje `LoadAssembliesAsync` metodu, která:

* Používá zprostředkovatele komunikace JS pro načítání sestavení prostřednictvím síťového volání.
* Načte sestavení do modulu runtime spuštěného na webovém sestavení v prohlížeči.

> [!NOTE]
> Implementace opožděného načítání rozhraní podporuje předvykreslování na serveru. Při předvykreslování se předpokládá, že budou načtena všechna sestavení, včetně těch, která jsou označena pro opožděné načítání.

### <a name="user-interaction-with-navigating-content"></a>Interakce uživatele s `<Navigating>` obsahem

Při načítání sestavení, která mohou trvat několik sekund, `Router` může komponenta uživateli indikovat, že dojde k přechodu na stránku:

* Přidejte [`@using`](xref:mvc/views/razor#using) direktivu pro <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> obor názvů.
* Přidejte `<Navigating>` značku do komponenty se značkami, které se mají zobrazit během událostí přechodu stránky.

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

### <a name="handle-cancellations-in-onnavigateasync"></a>Zpracovat zrušení v`OnNavigateAsync`

`NavigationContext`Objekt předaný `OnNavigateAsync` zpětnému volání obsahuje `CancellationToken` sadu, která je nastavena, když dojde k nové události navigace. `OnNavigateAsync`Zpětné volání musí vyvolat, když je tento token zrušení nastaven tak, aby se nepokračovalo `OnNavigateAsync` v provádění zpětného volání u zastaralé navigace.

Pokud uživatel přejde na trasu a a okamžitě ke směrování B, aplikace by neměla pokračovat ve spouštění `OnNavigateAsync` zpětného volání pro trasu a:

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
> Není aktivována, pokud je zrušený token zrušení v rámci zrušení `NavigationContext` zamýšleného chování, například vykreslování komponenty z předchozí navigace.

### <a name="complete-example"></a>Kompletní příklad

Následující kompletní `Router` Komponenta ukazuje načtení sestavení, `GrantImaharaRobotControls.dll` když uživatel přejde na `/robot` . Během přechodů stránky se uživateli zobrazí zpráva s uvedením stylu.

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

## <a name="troubleshoot"></a>Řešení potíží

* Pokud dojde k neočekávanému vykreslení (například je vykreslena komponenta z předchozí navigace), zkontrolujte, zda kód vyvolá výjimku, pokud je nastaven token zrušení.
* Pokud jsou sestavení stále načtena při spuštění aplikace, ověřte, zda je sestavení označeno jako opožděně načteno v souboru projektu.

## <a name="additional-resources"></a>Další materiály

* <xref:blazor/webassembly-performance-best-practices>
