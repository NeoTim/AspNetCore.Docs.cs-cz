---
title: BlazorŽivotní cyklus ASP.NET Core
author: guardrex
description: Naučte se používat Razor metody životního cyklu komponent v Blazor aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 00573f87b65e53a7bfd9cc2aed1d2ed7772b9a4a
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847608"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a>BlazorŽivotní cyklus ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

BlazorRozhraní zahrnuje synchronní a asynchronní metody životního cyklu. Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.

## <a name="lifecycle-methods"></a>Metody životního cyklu

### <a name="before-parameters-are-set"></a>Před nastavením parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> obsahuje sadu hodnot parametrů pro komponentu pokaždé, když <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> je volána.

Výchozí implementace <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sady nastaví hodnotu každé vlastnosti s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributem nebo, který má odpovídající hodnotu v <xref:Microsoft.AspNetCore.Components.ParameterView> . Parametry, které nemají odpovídající hodnotu v, <xref:Microsoft.AspNetCore.Components.ParameterView> jsou ponechány beze změny.

Pokud [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován. Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .

### <a name="component-initialization-methods"></a>Inicializační metody komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů z své nadřazené komponenty v <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> . 

Použijte <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.

Pro synchronní operaci popište <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :

```csharp
protected override void OnInitialized()
{
    ...
}
```

Chcete-li provést asynchronní operaci, přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> a použijte [`await`](/dotnet/csharp/language-reference/operators/await) operátor na operaci:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server aplikace, které [proprerender volání jejich obsahu](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_dvakrát_**:

* Jednou, když je komponenta zpočátku vykreslena jako součást stránky.
* Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.

Chcete-li zabránit tomu, aby se kód vývojáře v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Nespuštěn dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .

I když Blazor Server je aplikace předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně. Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .

### <a name="after-parameters-are-set"></a>Po nastavení parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> nebo <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> se nazývají:

* Po inicializaci komponenty v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> nebo <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .
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
> Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> události životního cyklu.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .

### <a name="after-component-render"></a>Po vykreslení komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> jsou volány po dokončení vykreslování součásti. V tuto chvíli se naplní odkazy na element a komponentu. Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.

`firstRender`Parametr pro <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :

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
> Asynchronní práce ihned po vykreslení musí nastat během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> události životního cyklu.
>
> I když vrátíte <xref:System.Threading.Tasks.Task> z <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy. To se vyhnete nekonečné smyčce vykreslování. Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nejsou volány během procesu předběžného vykreslování na serveru*. Metody jsou volány při interaktivním vykreslení komponenty po dokončení předzpracování. Když se aplikace předem vykreslí:

1. Komponenta se spustí na serveru, aby vytvořila nějaký statický kód HTML v odpovědi HTTP. V průběhu této fáze <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nejsou volány.
1. Když `blazor.server.js` nebo `blazor.webassembly.js` spustíte v prohlížeči, komponenta se restartuje v režimu interaktivního vykreslování. Po restartování komponenty se <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** zavolá, protože aplikace už není uvnitř fáze předvykreslování.

Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení. Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .

### <a name="suppress-ui-refreshing"></a>Potlačit aktualizaci uživatelského rozhraní

Přepsáním <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> potlačíte aktualizaci uživatelského rozhraní. Pokud se implementace vrátí `true` , uživatelské rozhraní se obnoví:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> je volána při každém vygenerování součásti.

I když <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> je přepsat, komponenta je vždy zpočátku vykreslena.

Další informace naleznete v tématu <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.

## <a name="state-changes"></a>Změny stavu

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> upozorní komponentu, že její stav se změnil. V případě potřeby volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> způsobí, že se komponenta znovu vykreslí.

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se nazývá automaticky pro <xref:Microsoft.AspNetCore.Components.EventCallback> metody. Další informace naleznete v tématu <xref:blazor/components/event-handling#eventcallback>.

## <a name="handle-incomplete-async-actions-at-render"></a>Zpracovat nedokončené asynchronní akce při vykreslení

Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny. Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty. Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány. Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), zatímco objekty jsou `null` .

V `FetchData` komponentě Blazor šablony <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> je přepsáno na asynchronně příjem dat předpovědi ( `forecasts` ). V takovém případě `forecasts` `null` se uživateli zobrazí zpráva o načítání. Po `Task` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> úspěšném dokončení se komponenta znovu vykreslí s aktualizovaným stavem.

`Pages/FetchData.razor` v Blazor Server šabloně:

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a>Ošetření chyb

Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .

## <a name="stateful-reconnection-after-prerendering"></a>Stav opětovného připojení po předvykreslování

V Blazor Server aplikaci, když <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> je <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , komponenta je zpočátku vykreslena jako součást stránky. Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní. Pokud [`OnInitialized{Async}`](#component-initialization-methods) je k dispozici metoda životního cyklu pro inicializaci komponenty, je metoda provedena *dvakrát*:

* Když se komponenta předem vykreslí.
* Po navázání připojení k serveru.

Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.

Abyste se vyhnuli scénáři dvojího vykreslování v Blazor Server aplikaci:

* Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.
* Použijte identifikátor při předvykreslování k uložení stavu součásti.
* Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.

Následující kód demonstruje aktualizaci `WeatherForecastService` v aplikaci založené na šablonách Blazor Server , která vylučuje dvojité vykreslování:

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

Další informace o naleznete v <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> tématu <xref:blazor/fundamentals/additional-scenarios#render-mode> .

## <a name="detect-when-the-app-is-prerendering"></a>Rozpoznat, kdy se aplikace předvykresluje

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a>Vyřazení komponent pomocí IDisposable

Pokud komponenta implementuje <xref:System.IDisposable> , je [ `Dispose` Metoda](/dotnet/standard/garbage-collection/implementing-dispose) volána, když je komponenta odebrána z uživatelského rozhraní. K vyřazení může dojít kdykoli, včetně při [inicializaci komponenty](#component-initialization-methods). Následující komponenta používá `@implements IDisposable` a `Dispose` metodu:

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
> Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> v `Dispose` není podporováno. <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.

Zruší odběr obslužných rutin událostí z událostí .NET. Následující příklady [ Blazor formulářů](xref:blazor/forms-validation) ukazují, jak odpojovat obslužnou rutinu události v `Dispose` metodě:

* Přístup k privátnímu poli a lambda

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* Přístup k privátní metodě

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a>Zrušit práci na pozadí

Komponenty často provádějí dlouhotrvající práci na pozadí, jako je například vytváření síťových volání ( <xref:System.Net.Http.HttpClient> ) a interakce s databázemi. Je žádoucí zastavit práci na pozadí, aby se v různých situacích šetřily systémové prostředky. Například asynchronní operace na pozadí se nezastaví automaticky, když uživatel přejde mimo komponentu.

Mezi další důvody, proč mohou pracovní položky na pozadí vyžadovat zrušení patří:

* Spuštění úlohy na pozadí bylo zahájeno s chybnými vstupními daty nebo parametry zpracování.
* Aktuální sada spuštěných pracovních položek na pozadí musí být nahrazena novou sadou pracovních položek.
* Priorita aktuálně spuštěných úloh musí být změněna.
* Aby bylo možné aplikaci znovu nasadit na server, je nutné ji vypnout.
* Prostředky serveru jsou omezené a vyžadují přeplánování pracovních položek na pozadí.

Implementace vzorové práce na pozadí v součásti:

* Použijte <xref:System.Threading.CancellationTokenSource> a <xref:System.Threading.CancellationToken> .
* Při [vyřazení součásti](#component-disposal-with-idisposable) a v jakémkoli bodě zrušení je požadováno ručním zrušením tokenu, voláním [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) signalizace, že by měla být zrušena činnost na pozadí.
* Po návratu asynchronního volání volejte <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na token.

V následujícím příkladu:

* `await Task.Delay(5000, cts.Token);` představuje dlouhodobě běžící asynchronní práci na pozadí.
* `BackgroundResourceMethod` představuje dlouhotrvající metodu na pozadí, která by neměla být spuštěna, pokud `Resource` je uvolněna před voláním metody.

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
