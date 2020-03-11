---
title: ASP.NET Core Blazor pokročilé scénáře
author: guardrex
description: Přečtěte si o rozšířených scénářích Blazor, včetně postupu ručního začlenění RenderTreeBuilder logiky do aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659451"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="c620f-103">ASP.NET Core pokročilé scénáře pro Blazor</span><span class="sxs-lookup"><span data-stu-id="c620f-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="c620f-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c620f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="c620f-105">Obslužná rutina okruhu serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="c620f-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="c620f-106">Blazor Server umožňuje kódu definovat *obslužnou rutinu okruhu*, která umožňuje spuštění kódu při změnách stavu okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="c620f-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="c620f-107">Obslužná rutina okruhu je implementována odvozením z `CircuitHandler` a registrací třídy v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="c620f-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="c620f-108">Následující příklad obslužné rutiny okruhu sleduje připojení Open Signal:</span><span class="sxs-lookup"><span data-stu-id="c620f-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

<span data-ttu-id="c620f-109">Obslužné rutiny okruhu jsou registrovány pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="c620f-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="c620f-110">Vymezené instance se vytvářejí pro jednotlivé instance okruhu.</span><span class="sxs-lookup"><span data-stu-id="c620f-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="c620f-111">Pomocí `TrackingCircuitHandler` v předchozím příkladu je vytvořena služba typu Singleton, protože stav všech okruhů musí být sledován:</span><span class="sxs-lookup"><span data-stu-id="c620f-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="c620f-112">Pokud metody obslužné rutiny vlastního okruhu vyvolávají neošetřenou výjimku, je výjimka závažná pro okruh serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="c620f-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="c620f-113">Chcete-li tolerovat výjimky v kódu obslužné rutiny nebo volané metody, zabalte kód v jednom nebo více příkazech [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="c620f-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="c620f-114">Když je okruh ukončený, protože uživatel je odpojený a rozhraní čistí stav okruhu, rozhraní uvolní obor DI okruhu.</span><span class="sxs-lookup"><span data-stu-id="c620f-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="c620f-115">Při likvidaci oboru se uvolní jakékoli služby DI Services s rozsahem okruhů, které implementují <xref:System.IDisposable?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="c620f-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="c620f-116">Pokud jakákoli služba DI vyvolá neošetřenou výjimku při vyřazení, rozhraní zaprotokoluje výjimku.</span><span class="sxs-lookup"><span data-stu-id="c620f-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="c620f-117">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="c620f-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="c620f-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.</span><span class="sxs-lookup"><span data-stu-id="c620f-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="c620f-119">Použití `RenderTreeBuilder` k vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="c620f-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="c620f-120">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="c620f-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="c620f-121">Vezměte v úvahu následující součást `PetDetails`, kterou je možné ručně vytvořit do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="c620f-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="c620f-122">V následujícím příkladu smyčka v metodě `CreateComponent` generuje tři komponenty `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="c620f-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="c620f-123">Při volání metod `RenderTreeBuilder` pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="c620f-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="c620f-124">Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání.</span><span class="sxs-lookup"><span data-stu-id="c620f-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="c620f-125">Při vytváření komponenty s metodami `RenderTreeBuilder` nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="c620f-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="c620f-126">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="c620f-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="c620f-127">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="c620f-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="c620f-128">součást `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="c620f-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="c620f-129">Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* operací vykreslování.</span><span class="sxs-lookup"><span data-stu-id="c620f-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="c620f-130">Jedná se o interní podrobnosti implementace Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="c620f-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="c620f-131">Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.</span><span class="sxs-lookup"><span data-stu-id="c620f-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="c620f-132">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="c620f-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="c620f-133">Soubory komponent Razor ( *. Razor*) jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="c620f-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="c620f-134">Kompilace je potenciální výhodou pro interpretaci kódu, protože krok kompilace lze použít k vložení informací, které zvyšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="c620f-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="c620f-135">Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*.</span><span class="sxs-lookup"><span data-stu-id="c620f-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="c620f-136">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="c620f-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="c620f-137">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="c620f-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="c620f-138">Vezměte v úvahu následující soubor součásti Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="c620f-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="c620f-139">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="c620f-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="c620f-140">Při prvním spuštění kódu, pokud je `someFlag` `true`, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="c620f-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="c620f-141">Pořadí</span><span class="sxs-lookup"><span data-stu-id="c620f-141">Sequence</span></span> | <span data-ttu-id="c620f-142">Typ</span><span class="sxs-lookup"><span data-stu-id="c620f-142">Type</span></span>      | <span data-ttu-id="c620f-143">Data</span><span class="sxs-lookup"><span data-stu-id="c620f-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c620f-144">0</span><span class="sxs-lookup"><span data-stu-id="c620f-144">0</span></span>        | <span data-ttu-id="c620f-145">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c620f-145">Text node</span></span> | <span data-ttu-id="c620f-146">První</span><span class="sxs-lookup"><span data-stu-id="c620f-146">First</span></span>  |
| <span data-ttu-id="c620f-147">1</span><span class="sxs-lookup"><span data-stu-id="c620f-147">1</span></span>        | <span data-ttu-id="c620f-148">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c620f-148">Text node</span></span> | <span data-ttu-id="c620f-149">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="c620f-149">Second</span></span> |

<span data-ttu-id="c620f-150">Představte si, že `someFlag` se budou `false`a kód se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="c620f-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="c620f-151">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="c620f-151">This time, the builder receives:</span></span>

| <span data-ttu-id="c620f-152">Pořadí</span><span class="sxs-lookup"><span data-stu-id="c620f-152">Sequence</span></span> | <span data-ttu-id="c620f-153">Typ</span><span class="sxs-lookup"><span data-stu-id="c620f-153">Type</span></span>       | <span data-ttu-id="c620f-154">Data</span><span class="sxs-lookup"><span data-stu-id="c620f-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="c620f-155">1</span><span class="sxs-lookup"><span data-stu-id="c620f-155">1</span></span>        | <span data-ttu-id="c620f-156">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c620f-156">Text node</span></span>  | <span data-ttu-id="c620f-157">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="c620f-157">Second</span></span> |

<span data-ttu-id="c620f-158">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:</span><span class="sxs-lookup"><span data-stu-id="c620f-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="c620f-159">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="c620f-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="c620f-160">Problém s tím, že se pořadová čísla generují programově</span><span class="sxs-lookup"><span data-stu-id="c620f-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="c620f-161">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="c620f-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="c620f-162">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="c620f-162">Now, the first output is:</span></span>

| <span data-ttu-id="c620f-163">Pořadí</span><span class="sxs-lookup"><span data-stu-id="c620f-163">Sequence</span></span> | <span data-ttu-id="c620f-164">Typ</span><span class="sxs-lookup"><span data-stu-id="c620f-164">Type</span></span>      | <span data-ttu-id="c620f-165">Data</span><span class="sxs-lookup"><span data-stu-id="c620f-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c620f-166">0</span><span class="sxs-lookup"><span data-stu-id="c620f-166">0</span></span>        | <span data-ttu-id="c620f-167">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c620f-167">Text node</span></span> | <span data-ttu-id="c620f-168">První</span><span class="sxs-lookup"><span data-stu-id="c620f-168">First</span></span>  |
| <span data-ttu-id="c620f-169">1</span><span class="sxs-lookup"><span data-stu-id="c620f-169">1</span></span>        | <span data-ttu-id="c620f-170">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c620f-170">Text node</span></span> | <span data-ttu-id="c620f-171">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="c620f-171">Second</span></span> |

<span data-ttu-id="c620f-172">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="c620f-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="c620f-173">`someFlag` je `false` při druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="c620f-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="c620f-174">Pořadí</span><span class="sxs-lookup"><span data-stu-id="c620f-174">Sequence</span></span> | <span data-ttu-id="c620f-175">Typ</span><span class="sxs-lookup"><span data-stu-id="c620f-175">Type</span></span>      | <span data-ttu-id="c620f-176">Data</span><span class="sxs-lookup"><span data-stu-id="c620f-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="c620f-177">0</span><span class="sxs-lookup"><span data-stu-id="c620f-177">0</span></span>        | <span data-ttu-id="c620f-178">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c620f-178">Text node</span></span> | <span data-ttu-id="c620f-179">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="c620f-179">Second</span></span> |

<span data-ttu-id="c620f-180">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="c620f-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="c620f-181">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="c620f-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="c620f-182">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="c620f-182">Remove the second text node.</span></span>

<span data-ttu-id="c620f-183">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` větve a smyčky byly obsaženy v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="c620f-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="c620f-184">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="c620f-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="c620f-185">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="c620f-185">This is a trivial example.</span></span> <span data-ttu-id="c620f-186">Ve složitějších případech se složitými a hluboce vnořenými strukturami a zejména s cykly jsou náklady na výkon obvykle vyšší.</span><span class="sxs-lookup"><span data-stu-id="c620f-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="c620f-187">Místo toho, aby ihned identifikovaly, které bloky nebo větve smyčky byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování.</span><span class="sxs-lookup"><span data-stu-id="c620f-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="c620f-188">To obvykle vede k sestavování delších úprav skriptů, protože rozdílový algoritmus je neinformován o tom, jak se stará a nová struktura vzájemně souvisí.</span><span class="sxs-lookup"><span data-stu-id="c620f-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="c620f-189">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="c620f-189">Guidance and conclusions</span></span>

* <span data-ttu-id="c620f-190">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="c620f-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="c620f-191">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="c620f-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="c620f-192">Nepište dlouhé bloky manuálně implementované `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="c620f-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="c620f-193">Preferovat soubory *. Razor* a umožněte kompilátoru, aby se zabývat pořadovým číslem.</span><span class="sxs-lookup"><span data-stu-id="c620f-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="c620f-194">Nemůžete-li se vyhnout ruční logice `RenderTreeBuilder`, rozdělte dlouhé bloky kódu do menších částí zabalených do `OpenRegion`/`CloseRegion` volání.</span><span class="sxs-lookup"><span data-stu-id="c620f-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="c620f-195">Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).</span><span class="sxs-lookup"><span data-stu-id="c620f-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="c620f-196">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="c620f-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="c620f-197">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="c620f-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="c620f-198">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="c620f-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="c620f-199"> používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="c620f-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="c620f-200">Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře vytvářející soubory *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="c620f-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="c620f-201">Provádění rozsáhlých přenosů dat v Blazor serverových aplikacích</span><span class="sxs-lookup"><span data-stu-id="c620f-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="c620f-202">V některých scénářích je nutné přenášet velké objemy dat mezi JavaScriptem a Blazor.</span><span class="sxs-lookup"><span data-stu-id="c620f-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="c620f-203">K přenosu velkých objemů dat se obvykle dochází v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="c620f-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="c620f-204">Rozhraní API pro systém souborů v prohlížeči slouží k nahrání nebo stažení souboru.</span><span class="sxs-lookup"><span data-stu-id="c620f-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="c620f-205">Je vyžadována interoperabilita s knihovnou třetích stran.</span><span class="sxs-lookup"><span data-stu-id="c620f-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="c620f-206">V Blazorm serveru je omezené omezení, které zabraňuje předávání jednoduchých zpráv, které mohou způsobit problémy s výkonem.</span><span class="sxs-lookup"><span data-stu-id="c620f-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="c620f-207">Při vývoji kódu, který přenáší data mezi JavaScriptem a Blazor, vezměte v úvahu následující pokyny:</span><span class="sxs-lookup"><span data-stu-id="c620f-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="c620f-208">Rozdělí data na menší části a datové segmenty se postupně odesílají, dokud server neobdrží všechna data.</span><span class="sxs-lookup"><span data-stu-id="c620f-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="c620f-209">Nepřiřazujte velké objekty v JavaScriptu C# a kódu.</span><span class="sxs-lookup"><span data-stu-id="c620f-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="c620f-210">Při odesílání nebo přijímání dat Neblokujte hlavní vlákno uživatelského rozhraní pro dlouhá období.</span><span class="sxs-lookup"><span data-stu-id="c620f-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="c620f-211">Uvolněte veškerou paměť, která je spotřebována při dokončení nebo zrušení procesu.</span><span class="sxs-lookup"><span data-stu-id="c620f-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="c620f-212">Vyjistěte následující další požadavky pro účely zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="c620f-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="c620f-213">Deklarujte maximální velikost souboru nebo dat, která se dají předat.</span><span class="sxs-lookup"><span data-stu-id="c620f-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="c620f-214">Deklarujte minimální rychlost nahrávání z klienta na server.</span><span class="sxs-lookup"><span data-stu-id="c620f-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="c620f-215">Po přijetí dat serverem můžou být tato data:</span><span class="sxs-lookup"><span data-stu-id="c620f-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="c620f-216">Dočasně uloženo do vyrovnávací paměti, dokud nebudou všechny segmenty shromažďovány.</span><span class="sxs-lookup"><span data-stu-id="c620f-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="c620f-217">Spotřebované hned.</span><span class="sxs-lookup"><span data-stu-id="c620f-217">Consumed immediately.</span></span> <span data-ttu-id="c620f-218">Data můžete například uložit hned do databáze nebo zapsat na disk při přijetí každého segmentu.</span><span class="sxs-lookup"><span data-stu-id="c620f-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="c620f-219">Následující třída pro odeslání souborů zpracovává zprostředkovatele komunikace JS s klientem.</span><span class="sxs-lookup"><span data-stu-id="c620f-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="c620f-220">Třída odeslání používá zprostředkovatele komunikace JS na:</span><span class="sxs-lookup"><span data-stu-id="c620f-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="c620f-221">Cyklické dotazování klienta o odeslání datového segmentu.</span><span class="sxs-lookup"><span data-stu-id="c620f-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="c620f-222">Zruší transakci, pokud vyprší časový limit pro cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="c620f-222">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="c620f-223">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c620f-223">In the preceding example:</span></span>

* <span data-ttu-id="c620f-224">`_maxBase64SegmentSize` je nastavená na `8192`, která se počítá z `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span><span class="sxs-lookup"><span data-stu-id="c620f-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="c620f-225">Rozhraní API pro správu paměti nižší úrovně v rozhraní .NET Core slouží k ukládání segmentů paměti na serveru v `_uploadedSegments`.</span><span class="sxs-lookup"><span data-stu-id="c620f-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="c620f-226">Metoda `ReceiveFile` se používá ke zpracování nahrávání prostřednictvím zprostředkovatele komunikace JS:</span><span class="sxs-lookup"><span data-stu-id="c620f-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="c620f-227">Velikost souboru je určena v bajtech přes interoperabilitu JS s `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span><span class="sxs-lookup"><span data-stu-id="c620f-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="c620f-228">Počet segmentů, které se mají přijmout, se vypočítá a uloží v `numberOfSegments`.</span><span class="sxs-lookup"><span data-stu-id="c620f-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="c620f-229">Segmenty jsou požadovány v `for` smyčka prostřednictvím zprostředkovatele komunikace JS s `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="c620f-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="c620f-230">Všechny segmenty, ale poslední, musí mít 8 192 bajtů před dekódováním.</span><span class="sxs-lookup"><span data-stu-id="c620f-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="c620f-231">Klient je nucen posílat data účinným způsobem.</span><span class="sxs-lookup"><span data-stu-id="c620f-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="c620f-232">Pro každý přijatý segment jsou kontroly provedeny před dekódováním pomocí <xref:System.Convert.TryFromBase64String*>.</span><span class="sxs-lookup"><span data-stu-id="c620f-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="c620f-233">Po dokončení nahrávání se datový proud s daty vrátí jako nový <xref:System.IO.Stream> (`SegmentedStream`).</span><span class="sxs-lookup"><span data-stu-id="c620f-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="c620f-234">Třída segmentace Stream zpřístupňuje seznam segmentů jako typ ReadOnly, který nelze prohledávat <xref:System.IO.Stream>:</span><span class="sxs-lookup"><span data-stu-id="c620f-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="c620f-235">Následující kód implementuje funkce JavaScriptu pro příjem dat:</span><span class="sxs-lookup"><span data-stu-id="c620f-235">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
