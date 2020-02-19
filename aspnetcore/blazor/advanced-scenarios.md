---
title: ASP.NET Core Blazor pokročilé scénáře
author: guardrex
description: Přečtěte si o rozšířených scénářích Blazor, včetně postupu ručního začlenění RenderTreeBuilder logiky do aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5e0618faa7b1b5e4cc15e30d9c16afaf7ccabaf0
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453259"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core pokročilé scénáře pro Blazor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

## <a name="manual-rendertreebuilder-logic"></a>Ruční logika RenderTreeBuilder

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.

> [!NOTE]
> Použití `RenderTreeBuilder` k vytváření komponent je pokročilý scénář. Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.

Vezměte v úvahu následující součást `PetDetails`, kterou je možné ručně vytvořit do jiné komponenty:

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

V následujícím příkladu smyčka v metodě `CreateComponent` generuje tři komponenty `PetDetails`. Při volání metod `RenderTreeBuilder` pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu. Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání. Při vytváření komponenty s metodami `RenderTreeBuilder` nekódujte pevně argumenty pro pořadová čísla. **Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.** Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

součást `BuiltContent`:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* operací vykreslování. Jedná se o interní podrobnosti implementace Blazor Framework. Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.

Soubory komponent Razor ( *. Razor*) jsou vždy kompilovány. Kompilace je potenciální výhodou pro interpretaci kódu, protože krok kompilace lze použít k vložení informací, které zvyšují výkon aplikace za běhu.

Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*. Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu. Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.

Vezměte v úvahu následující soubor součásti Razor ( *. Razor*):

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

Předchozí kód se zkompiluje jako následující:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Při prvním spuštění kódu, pokud je `someFlag` `true`, tvůrce obdrží:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | 1 sekunda |

Představte si, že `someFlag` se budou `false`a kód se znovu vykreslí. Tentokrát Tvůrce získá:

| Sequence | Typ       | Data   |
| :------: | ---------- | :----: |
| 1        | Textový uzel  | 1 sekunda |

Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:

* Odeberte první textový uzel.

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>Problém s tím, že se pořadová čísla generují programově

Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Teď je první výstup:

| Sequence | Typ      | Data   |
| :------: | --------- | :----: |
| 0        | Textový uzel | První  |
| 1        | Textový uzel | 1 sekunda |

Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy. `someFlag` je `false` při druhém vykreslování a výstup je:

| Sequence | Typ      | Data   |
| :------: | --------- | ------ |
| 0        | Textový uzel | 1 sekunda |

Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:

* Změňte hodnotu prvního textového uzlu na `Second`.
* Odeberte druhý textový uzel.

Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` větve a smyčky byly obsaženy v původním kódu. To vede ke rozdílu **dvakrát, jak dlouho** chcete.

Toto je triviální příklad. Ve složitějších případech se složitými a hluboce vnořenými strukturami a zejména s cykly jsou náklady na výkon obvykle vyšší. Místo toho, aby ihned identifikovaly, které bloky nebo větve smyčky byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování. To obvykle vede k sestavování delších úprav skriptů, protože rozdílový algoritmus je neinformován o tom, jak se stará a nová struktura vzájemně souvisí.

### <a name="guidance-and-conclusions"></a>Doprovodné materiály a závěry

* Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.
* Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.
* Nepište dlouhé bloky manuálně implementované `RenderTreeBuilder` logiky. Preferovat soubory *. Razor* a umožněte kompilátoru, aby se zabývat pořadovým číslem. Nemůžete-li se vyhnout ruční logice `RenderTreeBuilder`, rozdělte dlouhé bloky kódu do menších částí zabalených do `OpenRegion`/`CloseRegion` volání. Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).
* Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel. Počáteční hodnota a mezery jsou nepodstatné. Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu). 
* Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají. Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře vytvářející soubory *. Razor* .
