---
title: Životní cyklus Blazor ASP.NET Core
author: guardrex
description: Naučte se používat metody životního cyklu komponenty Razor v aplikacích ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 831f575afa6ce11d06c016d43ecd1bb59d09eab6
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218905"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>Životní cyklus Blazor ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Rozhraní Blazor zahrnuje synchronní a asynchronní metody životního cyklu. Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.

## <a name="lifecycle-methods"></a>Metody životního cyklu

### <a name="component-initialization-methods"></a>Inicializační metody komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů ze své nadřazené komponenty. Použijte `OnInitializedAsync`, když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.

Pro synchronní operaci popište `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Chcete-li provést asynchronní operaci, popište `OnInitializedAsync` a použijte klíčové slovo `await` pro operaci:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor serverových aplikací, které [proprerender volání jejich obsahu](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_dvakrát_** :

* Jednou, když je komponenta zpočátku vykreslena jako součást stránky.
* Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.

Chcete-li zabránit tomu, aby kód pro vývojáře v `OnInitializedAsync` běžel dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .

I když je aplikace Blazor serveru předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně. Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="before-parameters-are-set"></a>Před nastavením parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> obsahuje celou sadu hodnot parametrů pokaždé, když `SetParametersAsync` je volána.

Výchozí implementace `SetParametersAsync` nastaví hodnotu každé vlastnosti s atributem `[Parameter]` nebo `[CascadingParameter]`, který má odpovídající hodnotu v `ParameterView`. Parametry, které nemají odpovídající hodnotu v `ParameterView`, jsou ponechány beze změny.

Pokud `base.SetParametersAync` není vyvolán, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován. Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="after-parameters-are-set"></a>Po nastavení parametrů

jsou volány <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>:

* Při inicializaci komponenty a přijetí první sady parametrů ze své nadřazené komponenty.
* Po opětovném vykreslení nadřazené komponenty a dodání:
  * Pouze známé primitivní neměnné typy, u kterých se změnil alespoň jeden parametr.
  * Jakékoli parametry komplexního typu. Architektura nemůže zjistit, zda hodnoty parametru složitého typu jsou interně provedeny, takže se sada parametrů považuje za změněnou.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během události `OnParametersSetAsync` životního cyklu.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="after-component-render"></a>Po vykreslení komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> jsou volány po dokončení vykreslování součásti. V tuto chvíli se naplní odkazy na element a komponentu. Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.

Parametr `firstRender` pro `OnAfterRenderAsync` a `OnAfterRender`:

* Je nastaven na `true` při prvním vykreslení instance komponenty.
* Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> Asynchronní práce ihned po vykreslení musí nastat během `OnAfterRenderAsync` události životního cyklu.
>
> I když vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy. To se vyhnete nekonečné smyčce vykreslování. Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` a `OnAfterRenderAsync` *nejsou volány při předvykreslování na serveru.*

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="suppress-ui-refreshing"></a>Potlačit aktualizaci uživatelského rozhraní

Přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pro potlačení aktualizace uživatelského rozhraní. Pokud implementace vrátí `true`, uživatelské rozhraní se obnoví:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender` se volá pokaždé, když se komponenta vykreslí.

I v případě, že je `ShouldRender` přepsat, komponenta je vždy zpočátku vykreslena.

## <a name="state-changes"></a>Změny stavu

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> upozorní komponentu, jejíž stav byl změněn. Pokud je to možné, volání `StateHasChanged` způsobí, že se komponenta znovu vykreslí.

## <a name="handle-incomplete-async-actions-at-render"></a>Zpracovat nedokončené asynchronní akce při vykreslení

Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny. Objekty mohou být při provádění metody životního cyklu `null` nebo nedokončené s daty. Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány. Vykreslí prvky uživatelského rozhraní zástupného textu (například zprávu o načítání), zatímco objekty jsou `null`.

V `FetchData` součásti šablon Blazor je `OnInitializedAsync` přepsáno na asynchronně příjem dat předpovědi (`forecasts`). Pokud je `forecasts` `null`, uživateli se zobrazí zpráva o načítání. Jakmile se `Task` vrátí `OnInitializedAsync` dokončí, komponenta se znovu vykreslí s aktualizovaným stavem.

*Pages/FetchData. Razor* v šabloně serveru Blazor:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Vyřazení komponent pomocí IDisposable

Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní. Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> v `Dispose` není podporováno. `StateHasChanged` může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.

Zruší odběr obslužných rutin událostí z událostí .NET. Následující příklady [Blazor formuláře](xref:blazor/forms-validation) ukazují, jak odpojovat obslužnou rutinu události v metodě `Dispose`:

* Přístup k privátnímu poli a lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Přístup k privátní metodě

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Zpracování chyb

Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/handle-errors#lifecycle-methods>.

## <a name="stateful-reconnection-after-prerendering"></a>Stav opětovného připojení po předvykreslování

V aplikaci Blazor serveru, když je `RenderMode` `ServerPrerendered`, je součást zpočátku vykreslena jako součást stránky. Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní. Pokud je k dispozici metoda inicializace životního cyklu "inicializujd [{Async}](xref:blazor/lifecycle#component-initialization-methods) " pro inicializaci komponenty, je metoda provedena *dvakrát*:

* Když se komponenta předem vykreslí.
* Po navázání připojení k serveru.

Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.

Abyste se vyhnuli scénáři dvojího vykreslování v aplikaci Blazor serveru:

* Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.
* Použijte identifikátor při předvykreslování k uložení stavu součásti.
* Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.

Následující kód demonstruje aktualizované `WeatherForecastService` v aplikaci Blazor serveru založeném na šablonách, která vylučuje dvojité vykreslování:

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

Další informace o `RenderMode`najdete v tématu <xref:blazor/hosting-model-configuration#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Rozpoznat, kdy se aplikace předvykresluje

[!INCLUDE[](~/includes/blazor-prerendering.md)]
