---
title: Životní cyklus Blazor ASP.NET Core
author: guardrex
description: Naučte se používat metody životního cyklu komponenty Razor v aplikacích ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: 1482f6b2147c74b11836e8029401bb8bcb3cdb2d
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681411"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>Životní cyklus Blazor ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Rozhraní Blazor zahrnuje synchronní a asynchronní metody životního cyklu. Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.

## <a name="lifecycle-methods"></a>Metody životního cyklu

### <a name="component-initialization-methods"></a>Inicializační metody komponenty

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> spustit kód, který inicializuje komponentu. Tyto metody jsou volány pouze jednou při prvním vytvoření instance komponenty.

K provedení asynchronní operace použijte `OnInitializedAsync` a klíčové slovo `await` na operaci:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> Asynchronní práce během inicializace komponenty se musí vyskytnout během `OnInitializedAsync` události životního cyklu.

Pro synchronní operaci použijte `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

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

Výchozí implementace `SetParametersAsync` nastaví hodnotu každé vlastnosti dekorované pomocí atributu `[Parameter]` nebo `[CascadingParameter]`, který má odpovídající hodnotu v `ParameterView`. Parametry, které nemají odpovídající hodnotu v `ParameterView`, jsou ponechány beze změny.

Pokud `base.SetParametersAync` není vyvolán, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován. Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.

### <a name="after-parameters-are-set"></a>Po nastavení parametrů

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> jsou volány, když komponenta přijímá parametry z nadřazené položky a hodnoty jsou přiřazeny vlastnostem. Tyto metody jsou spouštěny po inicializaci komponenty a pokaždé, když jsou zadány nové hodnoty parametrů:

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

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Vyřazení komponent pomocí IDisposable

Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní. Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:

```csharp
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

## <a name="handle-errors"></a>Ošetření chyb

Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/handle-errors#lifecycle-methods>.
