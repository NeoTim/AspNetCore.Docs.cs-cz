---
title: Životní Blazor cyklus ASP.NET Core
author: guardrex
description: Naučte se používat Razor metody životního cyklu komponent Blazor v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 571f14247efe08ac6abbd6d1e2720656f94c213c
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967451"
---
# <a name="aspnet-core-blazor-lifecycle"></a>Životní Blazor cyklus ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Blazor Rozhraní zahrnuje synchronní a asynchronní metody životního cyklu. Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.

## <a name="lifecycle-methods"></a>Metody životního cyklu

### <a name="component-initialization-methods"></a>Inicializační metody komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů ze své nadřazené komponenty. Použijte `OnInitializedAsync` , když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.

Pro synchronní operaci popište `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Chcete-li provést asynchronní operaci, `OnInitializedAsync` přepište a `await` použijte klíčové slovo pro operaci:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorServerové aplikace, které [proprerender](xref:blazor/hosting-model-configuration#render-mode) volání `OnInitializedAsync` jejich obsahu **_dvakrát_**:

* Jednou, když je komponenta zpočátku vykreslena jako součást stránky.
* Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.

Chcete-li zabránit tomu `OnInitializedAsync` , aby se kód vývojáře v Nespuštěn dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .

I když Blazor je serverová aplikace předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně. Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="before-parameters-are-set"></a>Před nastavením parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>obsahuje celou sadu hodnot parametrů pokaždé, když `SetParametersAsync` je volána.

Výchozí implementace `SetParametersAsync` sady nastaví hodnotu každé vlastnosti s atributem `[Parameter]` nebo `[CascadingParameter]` , který má odpovídající hodnotu v. `ParameterView` Parametry, které nemají odpovídající hodnotu v `ParameterView` , jsou ponechány beze změny.

Pokud `base.SetParametersAync` není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován. Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="after-parameters-are-set"></a>Po nastavení parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> jsou volány:

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
> Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat `OnParametersSetAsync` během události životního cyklu.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="after-component-render"></a>Po vykreslení komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> jsou volány po dokončení vykreslování součásti. V tuto chvíli se naplní odkazy na element a komponentu. Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.

`firstRender` Parametr pro `OnAfterRenderAsync` a `OnAfterRender`:

* Je nastaven na `true` první, kdy je instance komponenty vykreslena.
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
> Asynchronní práce ihned po vykreslení musí nastat během události `OnAfterRenderAsync` životního cyklu.
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

`OnAfterRender`a `OnAfterRenderAsync` *nejsou volány při předvykreslování na serveru.*

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.

### <a name="suppress-ui-refreshing"></a>Potlačit aktualizaci uživatelského rozhraní

Přepsáním <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> potlačíte aktualizaci uživatelského rozhraní. Pokud se implementace vrátí `true`, uživatelské rozhraní se obnoví:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`je volána při každém vygenerování součásti.

I když `ShouldRender` je přepsat, komponenta je vždy zpočátku vykreslena.

## <a name="state-changes"></a>Změny stavu

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>upozorní komponentu, že její stav se změnil. V případě potřeby volání `StateHasChanged` způsobí, že se komponenta znovu vykreslí.

## <a name="handle-incomplete-async-actions-at-render"></a>Zpracovat nedokončené asynchronní akce při vykreslení

Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny. Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty. Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány. Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), `null`zatímco objekty jsou.

V `FetchData` Blazor komponentě šablony `OnInitializedAsync` je přepsáno na asynchronně příjem dat předpovědi (`forecasts`). `null`V takovém případě `forecasts` se uživateli zobrazí zpráva o načítání. `Task` Po `OnInitializedAsync` úspěšném dokončení se komponenta znovu vykreslí s aktualizovaným stavem.

*Pages/FetchData. Razor* v Blazor šabloně serveru:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Vyřazení komponent pomocí IDisposable

Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní. Následující komponenta používá `@implements IDisposable` a `Dispose` metodu:

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
> Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> v `Dispose` není podporováno. `StateHasChanged`může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.

Zruší odběr obslužných rutin událostí z událostí .NET. Následující [ Blazor příklady formulářů](xref:blazor/forms-validation) ukazují, jak odpojovat obslužnou rutinu události v `Dispose` metodě:

* Přístup k privátnímu poli a lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Přístup k privátní metodě

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Ošetření chyb

Informace o zpracování chyb během provádění metod životního cyklu naleznete <xref:blazor/handle-errors#lifecycle-methods>v tématu.

## <a name="stateful-reconnection-after-prerendering"></a>Stav opětovného připojení po předvykreslování

V Blazor serverové aplikaci, kde `RenderMode` je `ServerPrerendered`, se komponenta zpočátku generuje jako součást stránky staticky. Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní. Pokud je k dispozici metoda inicializace životního cyklu "inicializujd [{Async}](#component-initialization-methods) " pro inicializaci komponenty, je metoda provedena *dvakrát*:

* Když se komponenta předem vykreslí.
* Po navázání připojení k serveru.

Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.

Abyste se vyhnuli scénáři dvojího vykreslování Blazor v serverové aplikaci:

* Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.
* Použijte identifikátor při předvykreslování k uložení stavu součásti.
* Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.

Následující kód demonstruje aktualizaci `WeatherForecastService` v serverové aplikaci založené na Blazor šablonách, která vylučuje dvojité vykreslování:

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
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
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

Další informace o `RenderMode`naleznete v tématu <xref:blazor/hosting-model-configuration#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Rozpoznat, kdy se aplikace předvykresluje

[!INCLUDE[](~/includes/blazor-prerendering.md)]
