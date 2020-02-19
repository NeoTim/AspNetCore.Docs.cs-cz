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
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="61fbd-103">ASP.NET Core pokročilé scénáře pro Blazor</span><span class="sxs-lookup"><span data-stu-id="61fbd-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="61fbd-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="61fbd-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="61fbd-105">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="61fbd-105">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="61fbd-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.</span><span class="sxs-lookup"><span data-stu-id="61fbd-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="61fbd-107">Použití `RenderTreeBuilder` k vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="61fbd-107">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="61fbd-108">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="61fbd-108">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="61fbd-109">Vezměte v úvahu následující součást `PetDetails`, kterou je možné ručně vytvořit do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="61fbd-109">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="61fbd-110">V následujícím příkladu smyčka v metodě `CreateComponent` generuje tři komponenty `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="61fbd-110">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="61fbd-111">Při volání metod `RenderTreeBuilder` pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="61fbd-111">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="61fbd-112">Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání.</span><span class="sxs-lookup"><span data-stu-id="61fbd-112">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="61fbd-113">Při vytváření komponenty s metodami `RenderTreeBuilder` nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="61fbd-113">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="61fbd-114">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="61fbd-114">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="61fbd-115">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="61fbd-115">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="61fbd-116">součást `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="61fbd-116">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="61fbd-117">Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* operací vykreslování.</span><span class="sxs-lookup"><span data-stu-id="61fbd-117">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="61fbd-118">Jedná se o interní podrobnosti implementace Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="61fbd-118">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="61fbd-119">Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.</span><span class="sxs-lookup"><span data-stu-id="61fbd-119">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="61fbd-120">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="61fbd-120">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="61fbd-121">Soubory komponent Razor ( *. Razor*) jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="61fbd-121">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="61fbd-122">Kompilace je potenciální výhodou pro interpretaci kódu, protože krok kompilace lze použít k vložení informací, které zvyšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="61fbd-122">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="61fbd-123">Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*.</span><span class="sxs-lookup"><span data-stu-id="61fbd-123">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="61fbd-124">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="61fbd-124">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="61fbd-125">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="61fbd-125">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="61fbd-126">Vezměte v úvahu následující soubor součásti Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="61fbd-126">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="61fbd-127">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="61fbd-127">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="61fbd-128">Při prvním spuštění kódu, pokud je `someFlag` `true`, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="61fbd-128">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="61fbd-129">Sequence</span><span class="sxs-lookup"><span data-stu-id="61fbd-129">Sequence</span></span> | <span data-ttu-id="61fbd-130">Typ</span><span class="sxs-lookup"><span data-stu-id="61fbd-130">Type</span></span>      | <span data-ttu-id="61fbd-131">Data</span><span class="sxs-lookup"><span data-stu-id="61fbd-131">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="61fbd-132">0</span><span class="sxs-lookup"><span data-stu-id="61fbd-132">0</span></span>        | <span data-ttu-id="61fbd-133">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="61fbd-133">Text node</span></span> | <span data-ttu-id="61fbd-134">První</span><span class="sxs-lookup"><span data-stu-id="61fbd-134">First</span></span>  |
| <span data-ttu-id="61fbd-135">1</span><span class="sxs-lookup"><span data-stu-id="61fbd-135">1</span></span>        | <span data-ttu-id="61fbd-136">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="61fbd-136">Text node</span></span> | <span data-ttu-id="61fbd-137">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="61fbd-137">Second</span></span> |

<span data-ttu-id="61fbd-138">Představte si, že `someFlag` se budou `false`a kód se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="61fbd-138">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="61fbd-139">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="61fbd-139">This time, the builder receives:</span></span>

| <span data-ttu-id="61fbd-140">Sequence</span><span class="sxs-lookup"><span data-stu-id="61fbd-140">Sequence</span></span> | <span data-ttu-id="61fbd-141">Typ</span><span class="sxs-lookup"><span data-stu-id="61fbd-141">Type</span></span>       | <span data-ttu-id="61fbd-142">Data</span><span class="sxs-lookup"><span data-stu-id="61fbd-142">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="61fbd-143">1</span><span class="sxs-lookup"><span data-stu-id="61fbd-143">1</span></span>        | <span data-ttu-id="61fbd-144">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="61fbd-144">Text node</span></span>  | <span data-ttu-id="61fbd-145">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="61fbd-145">Second</span></span> |

<span data-ttu-id="61fbd-146">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:</span><span class="sxs-lookup"><span data-stu-id="61fbd-146">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="61fbd-147">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="61fbd-147">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="61fbd-148">Problém s tím, že se pořadová čísla generují programově</span><span class="sxs-lookup"><span data-stu-id="61fbd-148">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="61fbd-149">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="61fbd-149">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="61fbd-150">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="61fbd-150">Now, the first output is:</span></span>

| <span data-ttu-id="61fbd-151">Sequence</span><span class="sxs-lookup"><span data-stu-id="61fbd-151">Sequence</span></span> | <span data-ttu-id="61fbd-152">Typ</span><span class="sxs-lookup"><span data-stu-id="61fbd-152">Type</span></span>      | <span data-ttu-id="61fbd-153">Data</span><span class="sxs-lookup"><span data-stu-id="61fbd-153">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="61fbd-154">0</span><span class="sxs-lookup"><span data-stu-id="61fbd-154">0</span></span>        | <span data-ttu-id="61fbd-155">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="61fbd-155">Text node</span></span> | <span data-ttu-id="61fbd-156">První</span><span class="sxs-lookup"><span data-stu-id="61fbd-156">First</span></span>  |
| <span data-ttu-id="61fbd-157">1</span><span class="sxs-lookup"><span data-stu-id="61fbd-157">1</span></span>        | <span data-ttu-id="61fbd-158">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="61fbd-158">Text node</span></span> | <span data-ttu-id="61fbd-159">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="61fbd-159">Second</span></span> |

<span data-ttu-id="61fbd-160">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="61fbd-160">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="61fbd-161">`someFlag` je `false` při druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="61fbd-161">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="61fbd-162">Sequence</span><span class="sxs-lookup"><span data-stu-id="61fbd-162">Sequence</span></span> | <span data-ttu-id="61fbd-163">Typ</span><span class="sxs-lookup"><span data-stu-id="61fbd-163">Type</span></span>      | <span data-ttu-id="61fbd-164">Data</span><span class="sxs-lookup"><span data-stu-id="61fbd-164">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="61fbd-165">0</span><span class="sxs-lookup"><span data-stu-id="61fbd-165">0</span></span>        | <span data-ttu-id="61fbd-166">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="61fbd-166">Text node</span></span> | <span data-ttu-id="61fbd-167">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="61fbd-167">Second</span></span> |

<span data-ttu-id="61fbd-168">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="61fbd-168">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="61fbd-169">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="61fbd-169">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="61fbd-170">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="61fbd-170">Remove the second text node.</span></span>

<span data-ttu-id="61fbd-171">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` větve a smyčky byly obsaženy v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="61fbd-171">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="61fbd-172">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="61fbd-172">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="61fbd-173">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="61fbd-173">This is a trivial example.</span></span> <span data-ttu-id="61fbd-174">Ve složitějších případech se složitými a hluboce vnořenými strukturami a zejména s cykly jsou náklady na výkon obvykle vyšší.</span><span class="sxs-lookup"><span data-stu-id="61fbd-174">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="61fbd-175">Místo toho, aby ihned identifikovaly, které bloky nebo větve smyčky byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování.</span><span class="sxs-lookup"><span data-stu-id="61fbd-175">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="61fbd-176">To obvykle vede k sestavování delších úprav skriptů, protože rozdílový algoritmus je neinformován o tom, jak se stará a nová struktura vzájemně souvisí.</span><span class="sxs-lookup"><span data-stu-id="61fbd-176">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="61fbd-177">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="61fbd-177">Guidance and conclusions</span></span>

* <span data-ttu-id="61fbd-178">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="61fbd-178">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="61fbd-179">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="61fbd-179">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="61fbd-180">Nepište dlouhé bloky manuálně implementované `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="61fbd-180">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="61fbd-181">Preferovat soubory *. Razor* a umožněte kompilátoru, aby se zabývat pořadovým číslem.</span><span class="sxs-lookup"><span data-stu-id="61fbd-181">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="61fbd-182">Nemůžete-li se vyhnout ruční logice `RenderTreeBuilder`, rozdělte dlouhé bloky kódu do menších částí zabalených do `OpenRegion`/`CloseRegion` volání.</span><span class="sxs-lookup"><span data-stu-id="61fbd-182">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="61fbd-183">Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).</span><span class="sxs-lookup"><span data-stu-id="61fbd-183">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="61fbd-184">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="61fbd-184">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="61fbd-185">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="61fbd-185">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="61fbd-186">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="61fbd-186">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="61fbd-187"> používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="61fbd-187"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="61fbd-188">Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře vytvářející soubory *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="61fbd-188">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>
