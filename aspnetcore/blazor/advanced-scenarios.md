---
title: ASP.NET Blazor pokročilé scénáře Core
author: guardrex
description: Další informace o Blazorpokročilých scénářích v aplikaci , včetně toho, jak začlenit ruční logiku RenderTreeBuilder do aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659451"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="df910-103">ASP.NET core blazor pokročilé scénáře</span><span class="sxs-lookup"><span data-stu-id="df910-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="df910-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="df910-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a><span data-ttu-id="df910-105">Obslužná rutina obvodu serveru Blazor</span><span class="sxs-lookup"><span data-stu-id="df910-105">Blazor Server circuit handler</span></span>

<span data-ttu-id="df910-106">Blazor Server umožňuje kódu definovat *obslužnou rutinu okruhu*, která umožňuje spuštění kódu na změny stavu okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="df910-106">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="df910-107">Obslužná rutina okruhu `CircuitHandler` je implementována odvozením a registrací třídy v kontejneru služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="df910-107">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="df910-108">Následující příklad obslužné rutiny obvodu sleduje otevřená připojení SignalR:</span><span class="sxs-lookup"><span data-stu-id="df910-108">The following example of a circuit handler tracks open SignalR connections:</span></span>

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

<span data-ttu-id="df910-109">Obslužné rutiny obvodu jsou registrovány pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="df910-109">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="df910-110">Instance s vymezeným oborem jsou vytvořeny pro instanci okruhu.</span><span class="sxs-lookup"><span data-stu-id="df910-110">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="df910-111">Pomocí `TrackingCircuitHandler` v předchozím příkladu je vytvořena služba singleton, protože musí být sledován stav všech obvodů:</span><span class="sxs-lookup"><span data-stu-id="df910-111">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="df910-112">Pokud metody obslužné rutiny vlastního okruhu vyvolat neošetřené výjimky, výjimka je fatální blazor server obvodu.</span><span class="sxs-lookup"><span data-stu-id="df910-112">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="df910-113">Chcete-li tolerovat výjimky v kódu obslužné rutiny nebo volané metody, zabalte kód do jednoho nebo více příkazů [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) se zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="df910-113">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="df910-114">Pokud obvod končí, protože uživatel má odpojena a rozhraní je vyčištění stavu obvodu, rozhraní disponuje oboru DI obvodu.</span><span class="sxs-lookup"><span data-stu-id="df910-114">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="df910-115">Odstranění oboru disponuje všechny služby DI <xref:System.IDisposable?displayProperty=fullName>s rozsahem okruhu, které implementují .</span><span class="sxs-lookup"><span data-stu-id="df910-115">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="df910-116">Pokud všechny služby DI vyvolá neošetřené výjimky během likvidace, rozhraní protokoluje výjimku.</span><span class="sxs-lookup"><span data-stu-id="df910-116">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="df910-117">Ruční rendertreebuilder logika</span><span class="sxs-lookup"><span data-stu-id="df910-117">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="df910-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder`poskytuje metody pro manipulaci s komponentami a prvky, včetně vytváření komponent ručně v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="df910-118">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="df910-119">Použití `RenderTreeBuilder` k vytvoření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="df910-119">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="df910-120">Poškozená součást (například neuzavřená značkovací značka) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="df910-120">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="df910-121">Zvažte `PetDetails` následující součást, která může být ručně zabudována do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="df910-121">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="df910-122">V následujícím příkladu smyčka `CreateComponent` v metodě `PetDetails` generuje tři součásti.</span><span class="sxs-lookup"><span data-stu-id="df910-122">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="df910-123">Při `RenderTreeBuilder` volání metod pro vytvoření`OpenComponent` `AddAttribute`komponent (a ) pořadová čísla jsou čísla řádků kódu původu.</span><span class="sxs-lookup"><span data-stu-id="df910-123">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="df910-124">Blazorův algoritmus rozdílu závisí na pořadových číslech odpovídajících odlišným řádkům kódu, nikoli odlišným voláním volání.</span><span class="sxs-lookup"><span data-stu-id="df910-124">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="df910-125">Při vytváření komponenty pomocí `RenderTreeBuilder` metod pevně zakódujte argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="df910-125">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="df910-126">**Použití výpočtu nebo čítače ke generování pořadového čísla může vést ke snížení výkonu.**</span><span class="sxs-lookup"><span data-stu-id="df910-126">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="df910-127">Další informace naleznete v [části Pořadová čísla se vztahují k číslům řádků kódu a nikoli k oddílu pořadí provádění.](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)</span><span class="sxs-lookup"><span data-stu-id="df910-127">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="df910-128">`BuiltContent`Komponenty:</span><span class="sxs-lookup"><span data-stu-id="df910-128">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="df910-129">Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* vykreslování operací.</span><span class="sxs-lookup"><span data-stu-id="df910-129">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="df910-130">Jedná se o interní podrobnosti implementace rámce Blazor.</span><span class="sxs-lookup"><span data-stu-id="df910-130">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="df910-131">Tyto typy by měly být považovány za *nestabilní* a v budoucích verzích by se měly měnit.</span><span class="sxs-lookup"><span data-stu-id="df910-131">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="df910-132">Pořadová čísla se vztahují k číslům řádků kódu a nikoli k příkazu k provedení</span><span class="sxs-lookup"><span data-stu-id="df910-132">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="df910-133">Soubory komponent razor (*.razor*) jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="df910-133">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="df910-134">Kompilace je potenciální výhodou oproti interpretaci kódu, protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="df910-134">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="df910-135">Klíčovým příkladem těchto vylepšení jsou *pořadová čísla*.</span><span class="sxs-lookup"><span data-stu-id="df910-135">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="df910-136">Pořadová čísla označují do běhu, které výstupy pocházejí z, ze kterých odlišných a seřazených řádků kódu.</span><span class="sxs-lookup"><span data-stu-id="df910-136">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="df910-137">Runtime používá tyto informace ke generování efektivní strom rozdíly v lineární čas, což je mnohem rychlejší, než je obvykle možné pro obecný algoritmus rozdílu stromu.</span><span class="sxs-lookup"><span data-stu-id="df910-137">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="df910-138">Zvažte následující soubor komponenty Razor (*.razor):*</span><span class="sxs-lookup"><span data-stu-id="df910-138">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="df910-139">Předchozí kód zkompiluje na něco jako následující:</span><span class="sxs-lookup"><span data-stu-id="df910-139">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="df910-140">Při prvním spuštění kódu, pokud `someFlag` je `true`, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="df910-140">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="df910-141">Sequence</span><span class="sxs-lookup"><span data-stu-id="df910-141">Sequence</span></span> | <span data-ttu-id="df910-142">Typ</span><span class="sxs-lookup"><span data-stu-id="df910-142">Type</span></span>      | <span data-ttu-id="df910-143">Data</span><span class="sxs-lookup"><span data-stu-id="df910-143">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="df910-144">0</span><span class="sxs-lookup"><span data-stu-id="df910-144">0</span></span>        | <span data-ttu-id="df910-145">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="df910-145">Text node</span></span> | <span data-ttu-id="df910-146">První</span><span class="sxs-lookup"><span data-stu-id="df910-146">First</span></span>  |
| <span data-ttu-id="df910-147">1</span><span class="sxs-lookup"><span data-stu-id="df910-147">1</span></span>        | <span data-ttu-id="df910-148">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="df910-148">Text node</span></span> | <span data-ttu-id="df910-149">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="df910-149">Second</span></span> |

<span data-ttu-id="df910-150">Představte `someFlag` `false`si, že se stane , a značky je vykreslen znovu.</span><span class="sxs-lookup"><span data-stu-id="df910-150">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="df910-151">Tentokrát tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="df910-151">This time, the builder receives:</span></span>

| <span data-ttu-id="df910-152">Sequence</span><span class="sxs-lookup"><span data-stu-id="df910-152">Sequence</span></span> | <span data-ttu-id="df910-153">Typ</span><span class="sxs-lookup"><span data-stu-id="df910-153">Type</span></span>       | <span data-ttu-id="df910-154">Data</span><span class="sxs-lookup"><span data-stu-id="df910-154">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="df910-155">1</span><span class="sxs-lookup"><span data-stu-id="df910-155">1</span></span>        | <span data-ttu-id="df910-156">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="df910-156">Text node</span></span>  | <span data-ttu-id="df910-157">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="df910-157">Second</span></span> |

<span data-ttu-id="df910-158">Když runtime provede rozdíl, zjistí, že položka `0` v sekvenci byla odebrána, takže generuje následující triviální *editační skript*:</span><span class="sxs-lookup"><span data-stu-id="df910-158">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="df910-159">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="df910-159">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="df910-160">Problém s generováním pořadových čísel programově</span><span class="sxs-lookup"><span data-stu-id="df910-160">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="df910-161">Představte si, že místo toho jste napsali následující logiku tvůrce vykreslení:</span><span class="sxs-lookup"><span data-stu-id="df910-161">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="df910-162">Nyní je první výstup:</span><span class="sxs-lookup"><span data-stu-id="df910-162">Now, the first output is:</span></span>

| <span data-ttu-id="df910-163">Sequence</span><span class="sxs-lookup"><span data-stu-id="df910-163">Sequence</span></span> | <span data-ttu-id="df910-164">Typ</span><span class="sxs-lookup"><span data-stu-id="df910-164">Type</span></span>      | <span data-ttu-id="df910-165">Data</span><span class="sxs-lookup"><span data-stu-id="df910-165">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="df910-166">0</span><span class="sxs-lookup"><span data-stu-id="df910-166">0</span></span>        | <span data-ttu-id="df910-167">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="df910-167">Text node</span></span> | <span data-ttu-id="df910-168">První</span><span class="sxs-lookup"><span data-stu-id="df910-168">First</span></span>  |
| <span data-ttu-id="df910-169">1</span><span class="sxs-lookup"><span data-stu-id="df910-169">1</span></span>        | <span data-ttu-id="df910-170">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="df910-170">Text node</span></span> | <span data-ttu-id="df910-171">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="df910-171">Second</span></span> |

<span data-ttu-id="df910-172">Tento výsledek je totožný s předchozím případem, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="df910-172">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="df910-173">`someFlag`je `false` na druhé vykreslení a výstup je:</span><span class="sxs-lookup"><span data-stu-id="df910-173">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="df910-174">Sequence</span><span class="sxs-lookup"><span data-stu-id="df910-174">Sequence</span></span> | <span data-ttu-id="df910-175">Typ</span><span class="sxs-lookup"><span data-stu-id="df910-175">Type</span></span>      | <span data-ttu-id="df910-176">Data</span><span class="sxs-lookup"><span data-stu-id="df910-176">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="df910-177">0</span><span class="sxs-lookup"><span data-stu-id="df910-177">0</span></span>        | <span data-ttu-id="df910-178">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="df910-178">Text node</span></span> | <span data-ttu-id="df910-179">1 sekunda</span><span class="sxs-lookup"><span data-stu-id="df910-179">Second</span></span> |

<span data-ttu-id="df910-180">Tentokrát algoritmus diff zjistí, že došlo ke *dvěma* změnám a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="df910-180">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="df910-181">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="df910-181">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="df910-182">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="df910-182">Remove the second text node.</span></span>

<span data-ttu-id="df910-183">Generování pořadových čísel ztratilo všechny užitečné `if/else` informace o tom, kde byly větve a smyčky přítomny v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="df910-183">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="df910-184">To má za následek rozdíl **dvakrát tak dlouho jako** dříve.</span><span class="sxs-lookup"><span data-stu-id="df910-184">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="df910-185">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="df910-185">This is a trivial example.</span></span> <span data-ttu-id="df910-186">V realističtějších případech se složitými a hluboce vnořenými strukturami a zejména se smyčkami jsou náklady na výkon obvykle vyšší.</span><span class="sxs-lookup"><span data-stu-id="df910-186">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="df910-187">Namísto okamžitého určení, které smyčky bloky nebo větve byly vloženy nebo odstraněny, algoritmus diff musí recurse hluboko do vykreslovat stromy.</span><span class="sxs-lookup"><span data-stu-id="df910-187">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="df910-188">To obvykle vede k nutnosti vytvářet delší upravit skripty, protože algoritmus diff je nesprávně informován o tom, jak staré a nové struktury se vztahují k sobě navzájem.</span><span class="sxs-lookup"><span data-stu-id="df910-188">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="df910-189">Pokyny a závěry</span><span class="sxs-lookup"><span data-stu-id="df910-189">Guidance and conclusions</span></span>

* <span data-ttu-id="df910-190">Výkon aplikace trpí, pokud jsou dynamicky generována pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="df910-190">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="df910-191">Rozhraní framework nelze vytvořit vlastní pořadová čísla automaticky za běhu, protože potřebné informace neexistuje, pokud je zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="df910-191">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="df910-192">Nepište dlouhé bloky ručně implementované `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="df910-192">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="df910-193">Preferujte soubory *.razor* a povolte kompilátoru, aby se zabýval pořadovými čísly.</span><span class="sxs-lookup"><span data-stu-id="df910-193">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="df910-194">Pokud se nemůžete vyhnout `RenderTreeBuilder` ruční logice, rozdělte dlouhé bloky `OpenRegion` / `CloseRegion` kódu na menší části zabalené do volání.</span><span class="sxs-lookup"><span data-stu-id="df910-194">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="df910-195">Každá oblast má svůj vlastní samostatný prostor pořadových čísel, takže můžete restartovat z nuly (nebo jiné libovolné číslo) uvnitř každé oblasti.</span><span class="sxs-lookup"><span data-stu-id="df910-195">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="df910-196">Pokud jsou posloupná čísla pevně zakódována, algoritmus rozdílu vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="df910-196">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="df910-197">Počáteční hodnota a mezery jsou irelevantní.</span><span class="sxs-lookup"><span data-stu-id="df910-197">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="df910-198">Jednou z legitimních možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit o jedny nebo stovky (nebo jakýkoli preferovaný interval).</span><span class="sxs-lookup"><span data-stu-id="df910-198">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="df910-199">používá pořadová čísla, zatímco jiné architektury ui, které rozptylují strom, je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="df910-199"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="df910-200">Rozptylování je mnohem rychlejší při použití Blazor pořadových čísel a má tu výhodu, že kompiluje krok, který se automaticky zabývá pořadovými čísly pro vývojáře, kteří authoritují soubory *.razor.*</span><span class="sxs-lookup"><span data-stu-id="df910-200">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="df910-201">Provádění velkých přenosů dat v Blazor serverových aplikacích</span><span class="sxs-lookup"><span data-stu-id="df910-201">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="df910-202">V některých případech musí být mezi Jazykem BlazorJavaScript a .</span><span class="sxs-lookup"><span data-stu-id="df910-202">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="df910-203">K velkým přenosům dat obvykle dochází, když:</span><span class="sxs-lookup"><span data-stu-id="df910-203">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="df910-204">Rozhraní API systému souborů prohlížeče se používají k nahrání nebo stažení souboru.</span><span class="sxs-lookup"><span data-stu-id="df910-204">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="df910-205">Interop s knihovnou třetí strany je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="df910-205">Interop with a third party library is required.</span></span>

<span data-ttu-id="df910-206">V Blazor serveru je zavedeno omezení, které zabraňuje předávání jednu velké zprávy, které mohou mít za následek problémy s výkonem.</span><span class="sxs-lookup"><span data-stu-id="df910-206">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="df910-207">Při vývoji kódu, který přenáší data Blazormezi Jazykem JavaScript a :</span><span class="sxs-lookup"><span data-stu-id="df910-207">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="df910-208">Řez data na menší části a odeslat datové segmenty postupně, dokud všechna data přijata serverem.</span><span class="sxs-lookup"><span data-stu-id="df910-208">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="df910-209">Nepřidělujte velké objekty v kódu JavaScriptu a C#.</span><span class="sxs-lookup"><span data-stu-id="df910-209">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="df910-210">Při odesílání nebo přijímání dat neblokujte hlavní vlákno uživatelského rozhraní po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="df910-210">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="df910-211">Uvolněte všechny paměti spotřebované po dokončení nebo zrušení procesu.</span><span class="sxs-lookup"><span data-stu-id="df910-211">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="df910-212">Vynucujte následující další požadavky pro účely zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="df910-212">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="df910-213">Deklarujte maximální velikost souboru nebo dat, která může být předána.</span><span class="sxs-lookup"><span data-stu-id="df910-213">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="df910-214">Deklarujte minimální rychlost odesílání z klienta na server.</span><span class="sxs-lookup"><span data-stu-id="df910-214">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="df910-215">Po přijetí dat serverem mohou být data:</span><span class="sxs-lookup"><span data-stu-id="df910-215">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="df910-216">Dočasně uloženy ve vyrovnávací paměti, dokud všechny segmenty jsou shromažďovány.</span><span class="sxs-lookup"><span data-stu-id="df910-216">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="df910-217">Spotřebované okamžitě.</span><span class="sxs-lookup"><span data-stu-id="df910-217">Consumed immediately.</span></span> <span data-ttu-id="df910-218">Data mohou být například okamžitě uložena v databázi nebo zapsána na disk při přijetí každého segmentu.</span><span class="sxs-lookup"><span data-stu-id="df910-218">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="df910-219">Následující třída nahrazovače souborů zpracovává js interop s klientem.</span><span class="sxs-lookup"><span data-stu-id="df910-219">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="df910-220">Třída uploader používá js interop k:</span><span class="sxs-lookup"><span data-stu-id="df910-220">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="df910-221">Dotazování klienta odeslat datový segment.</span><span class="sxs-lookup"><span data-stu-id="df910-221">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="df910-222">Přerušit transakci, pokud časový interval dotazování.</span><span class="sxs-lookup"><span data-stu-id="df910-222">Abort the transaction if polling times out.</span></span>

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

<span data-ttu-id="df910-223">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="df910-223">In the preceding example:</span></span>

* <span data-ttu-id="df910-224">Je `_maxBase64SegmentSize` nastavena `8192`na , `_maxBase64SegmentSize = _segmentSize * 4 / 3`která je vypočtena z .</span><span class="sxs-lookup"><span data-stu-id="df910-224">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="df910-225">Rozhraní API pro správu paměti .NET core nízké úrovně se `_uploadedSegments`používají k ukládání segmentů paměti na serveru v .</span><span class="sxs-lookup"><span data-stu-id="df910-225">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="df910-226">Metoda `ReceiveFile` se používá ke zpracování nahrávání prostřednictvím JS interop:</span><span class="sxs-lookup"><span data-stu-id="df910-226">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="df910-227">Velikost souboru je určena v bajtů `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`prostřednictvím JS interop s .</span><span class="sxs-lookup"><span data-stu-id="df910-227">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="df910-228">Počet segmentů, které mají být `numberOfSegments`přijímány, se vypočítá a uloží do .</span><span class="sxs-lookup"><span data-stu-id="df910-228">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="df910-229">Segmenty jsou požadovány `for` ve smyčce prostřednictvím JS interop s `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="df910-229">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="df910-230">Všechny segmenty, ale poslední musí být 8 192 bajtů před dekódováním.</span><span class="sxs-lookup"><span data-stu-id="df910-230">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="df910-231">Klient je nucen odeslat data efektivním způsobem.</span><span class="sxs-lookup"><span data-stu-id="df910-231">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="df910-232">Pro každý přijatý segment se před <xref:System.Convert.TryFromBase64String*>dekódováním pomocí kontroly provádějí kontroly.</span><span class="sxs-lookup"><span data-stu-id="df910-232">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="df910-233">Datový proud s daty je <xref:System.IO.Stream> `SegmentedStream`vrácen jako nový ( ) po dokončení nahrávání.</span><span class="sxs-lookup"><span data-stu-id="df910-233">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="df910-234">Segmentovaná třída datového proudu zveřejňuje seznam segmentů jako nevyhledatelný <xref:System.IO.Stream>seznam pouze pro čtení :</span><span class="sxs-lookup"><span data-stu-id="df910-234">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

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

<span data-ttu-id="df910-235">Následující kód implementuje funkce Jazyka JavaScript pro příjem dat:</span><span class="sxs-lookup"><span data-stu-id="df910-235">The following code implements JavaScript functions to receive the data:</span></span>

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
