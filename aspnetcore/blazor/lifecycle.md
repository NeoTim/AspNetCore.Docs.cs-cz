---
title: ASP.NET Blazor životní cyklus jádra
author: guardrex
description: Přečtěte si, jak používat metody Blazor životního cyklu komponent Razor v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791455"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Blazor životní cyklus jádra

[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

Rámec Blazor zahrnuje synchronní a asynchronní metody životního cyklu. Přepište metody životního cyklu k provádění dalších operací s komponentami během inicializace a vykreslování součástí.

## <a name="lifecycle-methods"></a>Metody životního cyklu

### <a name="component-initialization-methods"></a>Metody inicializace komponent

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> jsou vyvolány při inicializování komponenty poté, co obdržela své počáteční parametry z nadřazené součásti. Použijte, `OnInitializedAsync` když komponenta provádí asynchronní operaci a měla by se aktualizovat po dokončení operace.

Pro synchronní operaci přepište `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Chcete-li provést asynchronní operaci, přepište `OnInitializedAsync` a použijte `await` klíčové slovo v operaci:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

BlazorServerové aplikace, které [předkonejte svůj](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_obsah, volají dvakrát_**:

* Jednou, když je komponenta zpočátku vykreslena staticky jako součást stránky.
* Podruhé, když prohlížeč naváže připojení zpět k serveru.

Chcete-li zabránit `OnInitializedAsync` spuštění kódu vývojáře v režimu dvakrát, přečtěte si [část Stavové opětovné připojení po předběžném vykreslování.](#stateful-reconnection-after-prerendering)

Zatímco Blazor aplikace Server je předběžné vykreslování, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Součásti může být nutné vykreslit odlišně, když jsou předem vykresleny. Další informace najdete v části [Zjistit, kdy je aplikace předvykačování.](#detect-when-the-app-is-prerendering)

Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci. Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)

### <a name="before-parameters-are-set"></a>Před nastavením parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>nastaví parametry dodané nadřazenou komponentou ve stromu vykreslení:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView>obsahuje celou sadu hodnot parametrů `SetParametersAsync` pokaždé, když je volána.

Výchozí implementace `SetParametersAsync` nastaví hodnotu každé `[Parameter]` vlastnosti s atributem nebo, `[CascadingParameter]` který má odpovídající hodnotu v `ParameterView`. Parametry, které nemají odpovídající hodnotu v `ParameterView` jsou ponechány beze změny.

Pokud `base.SetParametersAync` není vyvolána, vlastní kód můžete interpretovat hodnotu příchozí parametry v libovolném způsobem požadované. Například neexistuje žádný požadavek přiřadit příchozí parametry vlastnosti na třídu.

Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci. Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)

### <a name="after-parameters-are-set"></a>Po nastavení parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> nazývají se:

* Když je komponenta inicializována a získala první sadu parametrů z nadřazené součásti.
* Když se nadřazená součást znovu vykresluje a dodává:
  * Pouze známé primitivní neměnné typy, které alespoň jeden parametr změnil.
  * Všechny parametry se složitým typem. Rozhraní framework nemůže vědět, zda hodnoty parametru s komplexním typem zmutovaly interně, takže považuje sadu parametrů za změněnou.

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> Asynchronní práce při použití parametrů a hodnot `OnParametersSetAsync` vlastností musí dojít během události životního cyklu.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci. Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)

### <a name="after-component-render"></a>Po vykreslení komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> jsou volány po dokončení vykreslování komponenty. Odkazy na element y a komponenty jsou v tomto okamžiku naplněny. Tato fáze slouží k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace javascriptových knihoven třetích stran, které pracují s vykreslené prvky DOM.

Parametr `firstRender` pro `OnAfterRenderAsync` `OnAfterRender`a:

* Je nastavena na `true` první, kdy je instance komponenty vykreslena.
* Lze zajistit, že inicializační práce se provádí pouze jednou.

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
> Asynchronní práce ihned po vykreslování musí dojít během události `OnAfterRenderAsync` životního cyklu.
>
> I v případě, že vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslení pro komponentu po dokončení tohoto úkolu. To to je, aby se zabránilo nekonečné vykreslení smyčky. Liší se od jiných metod životního cyklu, které plánují další cyklus vykreslení po dokončení vrácené úlohy.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender`a `OnAfterRenderAsync` *nejsou volány při předběžném vykreslování na serveru.*

Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci. Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)

### <a name="suppress-ui-refreshing"></a>Potlačit aktualizaci uI

Přepsáním <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> potlačíte, abyste potlačili aktualizaci uI. Pokud se `true`vrátí implementace , ui se aktualizuje:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender`při každém vykreslení komponenty.

I `ShouldRender` když je přepsána, komponenta je vždy zpočátku vykreslena.

## <a name="state-changes"></a>Změny stavu

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>upozorní komponentu, že se její stav změnil. Pokud je `StateHasChanged` to možné, volání způsobí, že součást má být rerenderována.

## <a name="handle-incomplete-async-actions-at-render"></a>Zpracování neúplných asynchronních akcí při vykreslení

Asynchronní akce prováděné v událostech životního cyklu pravděpodobně nebyly dokončeny před vykreslením součásti. Objekty `null` mohou být nebo nezcela naplněny daty při provádění metody životního cyklu. Zadejte logiku vykreslování a potvrďte, že objekty jsou inicializovány. Vykreslujte zástupné prvky uživatelského `null`rozhraní (například načítanou zprávu), zatímco objekty jsou .

V `FetchData` součásti Blazor šablon je `OnInitializedAsync` přepsána tak, aby asychronně přijínala data prognózy (`forecasts`). Pokud `forecasts` `null`je , zobrazí se uživateli načítaná zpráva. Po `Task` vrácena `OnInitializedAsync` dokončena je komponenta překreslovat s aktualizovaným stavem.

*Stránky/FetchData.razor* v Blazor šabloně Server:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Likvidace součástí s IDisposable

Pokud součást implementuje <xref:System.IDisposable>, Dispose [metoda](/dotnet/standard/garbage-collection/implementing-dispose) je volána při odebrání komponenty z ui. Používá následující `@implements IDisposable` komponentu `Dispose` a metodu:

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
> Ozvát <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` se není podporováno. `StateHasChanged`může být vyvolána jako součást stržení vykreslovacího modulu, takže požadování aktualizací ui v tomto okamžiku není podporováno.

Odhlášení obslužných rutin událostí z událostí rozhraní .NET. Následující [ Blazor příklady formuláře](xref:blazor/forms-validation) ukazují, jak odpojit obslužnou rutinu události v metodě: `Dispose`

* Přístup založený na soukromém poli a lambdě

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Přístup soukromé metody

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a>Ošetření chyb

Informace o zpracování chyb během provádění <xref:blazor/handle-errors#lifecycle-methods>metody životního cyklu naleznete v tématu .

## <a name="stateful-reconnection-after-prerendering"></a>Stavové opětovné připojení po předběžném vykreslování

V Blazor aplikaci `RenderMode` Server, když je `ServerPrerendered`, je komponenta zpočátku vykreslena staticky jako součást stránky. Jakmile prohlížeč naváže připojení zpět k serveru, komponenta je *vykreslena znovu*a komponenta je nyní interaktivní. Pokud je k dispozici metoda životního cyklu [OnInitialized{Async}](#component-initialization-methods) pro inicializaci komponenty, je metoda provedena *dvakrát*:

* Když je komponenta předvykrestoustaticky.
* Po navázání připojení k serveru.

To může mít za následek znatelnou změnu dat zobrazených v unovém unovém řízení při nakonec vykreslení komponenty.

Chcete-li se vyhnout scénáři dvojitého vykreslování Blazor v aplikaci Server:

* Předat identifikátor, který lze použít k ukládání do mezipaměti stavu během předběžného vykreslování a načíst stav po restartování aplikace.
* Pomocí identifikátoru při předběžném vykreslování uložte stav komponenty.
* Použijte identifikátor po předběžném vykreslování k načtení stavu uloženého v mezipaměti.

Následující kód ukazuje aktualizovanou `WeatherForecastService` aplikaci Server založenou na Blazor šablonách, která zabraňuje dvojitému vykreslování:

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

Další informace naleznete `RenderMode`v <xref:blazor/hosting-model-configuration#render-mode>tématu .

## <a name="detect-when-the-app-is-prerendering"></a>Zjištění, kdy je aplikace předvykreslování

[!INCLUDE[](~/includes/blazor-prerendering.md)]
