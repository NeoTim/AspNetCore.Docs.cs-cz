---
<span data-ttu-id="24a20-101">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-101">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-103">'Blazor'</span></span>
- <span data-ttu-id="24a20-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-104">'Identity'</span></span>
- <span data-ttu-id="24a20-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-106">'Razor'</span></span>
- <span data-ttu-id="24a20-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="24a20-108">ASP.NET Core Blazor pokročilé scénáře</span><span class="sxs-lookup"><span data-stu-id="24a20-108">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="24a20-109">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="24a20-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="blazor-server-circuit-handler"></a>Blazor<span data-ttu-id="24a20-110">Obslužná rutina okruhu serveru</span><span class="sxs-lookup"><span data-stu-id="24a20-110"> Server circuit handler</span></span>

Blazor<span data-ttu-id="24a20-111">Server umožňuje kódu definovat *obslužnou rutinu okruhu*, která umožňuje spuštění kódu při změnách stavu okruhu uživatele.</span><span class="sxs-lookup"><span data-stu-id="24a20-111"> Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="24a20-112">Obslužná rutina okruhu je implementována odvozením z `CircuitHandler` a registrací třídy v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="24a20-112">A circuit handler is implemented by deriving from `CircuitHandler` and registering the class in the app's service container.</span></span> <span data-ttu-id="24a20-113">Následující příklad obslužné rutiny okruhu sleduje otevřená SignalR připojení:</span><span class="sxs-lookup"><span data-stu-id="24a20-113">The following example of a circuit handler tracks open SignalR connections:</span></span>

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => circuits.Count;
}
```

<span data-ttu-id="24a20-114">Obslužné rutiny okruhu jsou registrovány pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="24a20-114">Circuit handlers are registered using DI.</span></span> <span data-ttu-id="24a20-115">Vymezené instance se vytvářejí pro jednotlivé instance okruhu.</span><span class="sxs-lookup"><span data-stu-id="24a20-115">Scoped instances are created per instance of a circuit.</span></span> <span data-ttu-id="24a20-116">Pomocí `TrackingCircuitHandler` v předchozím příkladu je vytvořena singleton služba, protože stav všech okruhů musí být sledován:</span><span class="sxs-lookup"><span data-stu-id="24a20-116">Using the `TrackingCircuitHandler` in the preceding example, a singleton service is created because the state of all circuits must be tracked:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

<span data-ttu-id="24a20-117">Pokud metody obslužné rutiny vlastního okruhu vyvolávají neošetřenou výjimku, je výjimka závažná pro Blazor okruh serveru.</span><span class="sxs-lookup"><span data-stu-id="24a20-117">If a custom circuit handler's methods throw an unhandled exception, the exception is fatal to the Blazor Server circuit.</span></span> <span data-ttu-id="24a20-118">Chcete-li tolerovat výjimky v kódu obslužné rutiny nebo volané metody, zabalte kód v jednom nebo více příkazech [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) s zpracováním chyb a protokolováním.</span><span class="sxs-lookup"><span data-stu-id="24a20-118">To tolerate exceptions in a handler's code or called methods, wrap the code in one or more [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span>

<span data-ttu-id="24a20-119">Když je okruh ukončený, protože uživatel je odpojený a rozhraní čistí stav okruhu, rozhraní uvolní obor DI okruhu.</span><span class="sxs-lookup"><span data-stu-id="24a20-119">When a circuit ends because a user has disconnected and the framework is cleaning up the circuit state, the framework disposes of the circuit's DI scope.</span></span> <span data-ttu-id="24a20-120">Při likvidaci oboru se uvolní jakékoli DI služby, které implementují okruhy s rozsahem <xref:System.IDisposable?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="24a20-120">Disposing the scope disposes any circuit-scoped DI services that implement <xref:System.IDisposable?displayProperty=fullName>.</span></span> <span data-ttu-id="24a20-121">Pokud jakákoli služba DI vyvolá neošetřenou výjimku při vyřazení, rozhraní zaprotokoluje výjimku.</span><span class="sxs-lookup"><span data-stu-id="24a20-121">If any DI service throws an unhandled exception during disposal, the framework logs the exception.</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="24a20-122">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="24a20-122">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="24a20-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>poskytuje metody pro práci s komponentami a prvky, včetně sestavování komponent ručně v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="24a20-123"><xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="24a20-124">Použití aplikace <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> k vytvoření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="24a20-124">Use of <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to create components is an advanced scenario.</span></span> <span data-ttu-id="24a20-125">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="24a20-125">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="24a20-126">Vezměte v úvahu následující `PetDetails` součást, kterou je možné ručně vytvořit do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="24a20-126">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="24a20-127">V následujícím příkladu smyčka v `CreateComponent` metodě generuje tři `PetDetails` komponenty.</span><span class="sxs-lookup"><span data-stu-id="24a20-127">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="24a20-128">Při volání <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metod pro vytvoření komponent ( `OpenComponent` a `AddAttribute` ) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="24a20-128">When calling <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="24a20-129">BlazorRozdílový algoritmus spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, nikoli rozdíl volání volání.</span><span class="sxs-lookup"><span data-stu-id="24a20-129">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="24a20-130">Při vytváření komponenty s <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> metodami nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="24a20-130">When creating a component with <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="24a20-131">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="24a20-131">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="24a20-132">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="24a20-132">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="24a20-133">`BuiltContent`část</span><span class="sxs-lookup"><span data-stu-id="24a20-133">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="24a20-134">Typy v nástroji <xref:Microsoft.AspNetCore.Components.RenderTree> umožňují zpracování *výsledků* operací vykreslování.</span><span class="sxs-lookup"><span data-stu-id="24a20-134">The types in <xref:Microsoft.AspNetCore.Components.RenderTree> allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="24a20-135">Jedná se o interní podrobnosti Blazor implementace rozhraní.</span><span class="sxs-lookup"><span data-stu-id="24a20-135">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="24a20-136">Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.</span><span class="sxs-lookup"><span data-stu-id="24a20-136">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="24a20-137">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="24a20-137">Sequence numbers relate to code line numbers and not execution order</span></span>

Razor<span data-ttu-id="24a20-138">soubory komponent (*. Razor*) jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="24a20-138"> component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="24a20-139">Kompilace je potenciální výhodou pro interpretaci kódu, protože krok kompilace lze použít k vložení informací, které zvyšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="24a20-139">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="24a20-140">Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*.</span><span class="sxs-lookup"><span data-stu-id="24a20-140">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="24a20-141">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="24a20-141">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="24a20-142">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="24a20-142">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="24a20-143">Vezměte v úvahu následující Razor soubor součásti (*. Razor*):</span><span class="sxs-lookup"><span data-stu-id="24a20-143">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="24a20-144">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="24a20-144">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="24a20-145">V případě, že se kód spustí poprvé, `someFlag` v případě `true` , že je, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="24a20-145">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="24a20-146">Sequence</span><span class="sxs-lookup"><span data-stu-id="24a20-146">Sequence</span></span> | <span data-ttu-id="24a20-147">Typ</span><span class="sxs-lookup"><span data-stu-id="24a20-147">Type</span></span>      | <span data-ttu-id="24a20-148">Data</span><span class="sxs-lookup"><span data-stu-id="24a20-148">Data</span></span>   |
| :---
<span data-ttu-id="24a20-149">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-149">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-150">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-150">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-151">'Blazor'</span></span>
- <span data-ttu-id="24a20-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-152">'Identity'</span></span>
- <span data-ttu-id="24a20-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-154">'Razor'</span></span>
- <span data-ttu-id="24a20-155">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-155">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-156">---: | ---title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční RenderTreeBuilder logiky do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-156">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-157">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-157">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-158">'Blazor'</span></span>
- <span data-ttu-id="24a20-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-159">'Identity'</span></span>
- <span data-ttu-id="24a20-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-161">'Razor'</span></span>
- <span data-ttu-id="24a20-162">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="24a20-163">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-163">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-164">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-164">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-165">'Blazor'</span></span>
- <span data-ttu-id="24a20-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-166">'Identity'</span></span>
- <span data-ttu-id="24a20-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-168">'Razor'</span></span>
- <span data-ttu-id="24a20-169">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-169">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-170">----- | :----: | | 0 | Textový uzel | První | | 1 | Textový uzel | Druhý |</span><span class="sxs-lookup"><span data-stu-id="24a20-170">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="24a20-171">Představte si, že se `someFlag` `false` zobrazí a značka se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="24a20-171">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="24a20-172">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="24a20-172">This time, the builder receives:</span></span>

| <span data-ttu-id="24a20-173">Sequence</span><span class="sxs-lookup"><span data-stu-id="24a20-173">Sequence</span></span> | <span data-ttu-id="24a20-174">Typ</span><span class="sxs-lookup"><span data-stu-id="24a20-174">Type</span></span>       | <span data-ttu-id="24a20-175">Data</span><span class="sxs-lookup"><span data-stu-id="24a20-175">Data</span></span>   |
| :---
<span data-ttu-id="24a20-176">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-176">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-177">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-178">'Blazor'</span></span>
- <span data-ttu-id="24a20-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-179">'Identity'</span></span>
- <span data-ttu-id="24a20-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-181">'Razor'</span></span>
- <span data-ttu-id="24a20-182">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-182">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-183">---: | ---title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční RenderTreeBuilder logiky do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-183">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-184">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-185">'Blazor'</span></span>
- <span data-ttu-id="24a20-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-186">'Identity'</span></span>
- <span data-ttu-id="24a20-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-188">'Razor'</span></span>
- <span data-ttu-id="24a20-189">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="24a20-190">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-190">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-191">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-192">'Blazor'</span></span>
- <span data-ttu-id="24a20-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-193">'Identity'</span></span>
- <span data-ttu-id="24a20-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-195">'Razor'</span></span>
- <span data-ttu-id="24a20-196">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="24a20-197">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-197">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-198">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-199">'Blazor'</span></span>
- <span data-ttu-id="24a20-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-200">'Identity'</span></span>
- <span data-ttu-id="24a20-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-202">'Razor'</span></span>
- <span data-ttu-id="24a20-203">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-203">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-204">----- | :----: | | 1 | Textový uzel | Druhý |</span><span class="sxs-lookup"><span data-stu-id="24a20-204">----- | :----: | | 1        | Text node  | Second |</span></span>

<span data-ttu-id="24a20-205">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující *skript*triviálního úprav:</span><span class="sxs-lookup"><span data-stu-id="24a20-205">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="24a20-206">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="24a20-206">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="24a20-207">Problém s tím, že se pořadová čísla generují programově</span><span class="sxs-lookup"><span data-stu-id="24a20-207">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="24a20-208">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="24a20-208">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="24a20-209">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="24a20-209">Now, the first output is:</span></span>

| <span data-ttu-id="24a20-210">Sequence</span><span class="sxs-lookup"><span data-stu-id="24a20-210">Sequence</span></span> | <span data-ttu-id="24a20-211">Typ</span><span class="sxs-lookup"><span data-stu-id="24a20-211">Type</span></span>      | <span data-ttu-id="24a20-212">Data</span><span class="sxs-lookup"><span data-stu-id="24a20-212">Data</span></span>   |
| :---
<span data-ttu-id="24a20-213">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-213">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-214">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-214">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-215">'Blazor'</span></span>
- <span data-ttu-id="24a20-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-216">'Identity'</span></span>
- <span data-ttu-id="24a20-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-218">'Razor'</span></span>
- <span data-ttu-id="24a20-219">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-219">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-220">---: | ---title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční RenderTreeBuilder logiky do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-220">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-221">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-221">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-222">'Blazor'</span></span>
- <span data-ttu-id="24a20-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-223">'Identity'</span></span>
- <span data-ttu-id="24a20-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-225">'Razor'</span></span>
- <span data-ttu-id="24a20-226">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="24a20-227">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-227">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-228">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-228">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-229">'Blazor'</span></span>
- <span data-ttu-id="24a20-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-230">'Identity'</span></span>
- <span data-ttu-id="24a20-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-232">'Razor'</span></span>
- <span data-ttu-id="24a20-233">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-233">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-234">----- | :----: | | 0 | Textový uzel | První | | 1 | Textový uzel | Druhý |</span><span class="sxs-lookup"><span data-stu-id="24a20-234">----- | :----: | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="24a20-235">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="24a20-235">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="24a20-236">`someFlag`je `false` ve druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="24a20-236">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="24a20-237">Sequence</span><span class="sxs-lookup"><span data-stu-id="24a20-237">Sequence</span></span> | <span data-ttu-id="24a20-238">Typ</span><span class="sxs-lookup"><span data-stu-id="24a20-238">Type</span></span>      | <span data-ttu-id="24a20-239">Data</span><span class="sxs-lookup"><span data-stu-id="24a20-239">Data</span></span>   |
| :---
<span data-ttu-id="24a20-240">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-240">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-241">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-241">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-242">'Blazor'</span></span>
- <span data-ttu-id="24a20-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-243">'Identity'</span></span>
- <span data-ttu-id="24a20-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-245">'Razor'</span></span>
- <span data-ttu-id="24a20-246">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-246">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-247">---: | ---title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční RenderTreeBuilder logiky do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-247">---: | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-248">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-248">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-249">'Blazor'</span></span>
- <span data-ttu-id="24a20-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-250">'Identity'</span></span>
- <span data-ttu-id="24a20-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-252">'Razor'</span></span>
- <span data-ttu-id="24a20-253">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="24a20-254">title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční logiky RenderTreeBuilder do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-254">title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-255">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-255">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-256">'Blazor'</span></span>
- <span data-ttu-id="24a20-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-257">'Identity'</span></span>
- <span data-ttu-id="24a20-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-259">'Razor'</span></span>
- <span data-ttu-id="24a20-260">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-260">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-261">----- | ---title: ' ASP.NET Core Blazor pokročilé scénáře ' Autor: Popis: ' informace o rozšířených scénářích v nástroji Blazor , včetně postupu začlenění ruční RenderTreeBuilder logiky do aplikace. '</span><span class="sxs-lookup"><span data-stu-id="24a20-261">----- | --- title: 'ASP.NET Core Blazor advanced scenarios' author: description: 'Learn about advanced scenarios in Blazor, including how to incorporate manual RenderTreeBuilder logic into an app.'</span></span>
<span data-ttu-id="24a20-262">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="24a20-262">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="24a20-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="24a20-263">'Blazor'</span></span>
- <span data-ttu-id="24a20-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="24a20-264">'Identity'</span></span>
- <span data-ttu-id="24a20-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="24a20-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="24a20-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="24a20-266">'Razor'</span></span>
- <span data-ttu-id="24a20-267">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="24a20-267">'SignalR' uid:</span></span> 

<span data-ttu-id="24a20-268">--- | | 0 | Textový uzel | Druhý |</span><span class="sxs-lookup"><span data-stu-id="24a20-268">--- | | 0        | Text node | Second |</span></span>

<span data-ttu-id="24a20-269">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="24a20-269">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="24a20-270">Změňte hodnotu prvního textového uzlu na `Second` .</span><span class="sxs-lookup"><span data-stu-id="24a20-270">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="24a20-271">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="24a20-271">Remove the second text node.</span></span>

<span data-ttu-id="24a20-272">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` byly větve a cykly přítomny v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="24a20-272">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="24a20-273">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="24a20-273">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="24a20-274">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="24a20-274">This is a trivial example.</span></span> <span data-ttu-id="24a20-275">Ve složitějších případech se složitými a hluboce vnořenými strukturami a zejména s cykly jsou náklady na výkon obvykle vyšší.</span><span class="sxs-lookup"><span data-stu-id="24a20-275">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="24a20-276">Místo toho, aby ihned identifikovaly, které bloky nebo větve smyčky byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování.</span><span class="sxs-lookup"><span data-stu-id="24a20-276">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="24a20-277">To obvykle vede k sestavování delších úprav skriptů, protože rozdílový algoritmus je neinformován o tom, jak se stará a nová struktura vzájemně souvisí.</span><span class="sxs-lookup"><span data-stu-id="24a20-277">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="24a20-278">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="24a20-278">Guidance and conclusions</span></span>

* <span data-ttu-id="24a20-279">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="24a20-279">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="24a20-280">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="24a20-280">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="24a20-281">Nepište dlouhé bloky ručně implementované <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logiky.</span><span class="sxs-lookup"><span data-stu-id="24a20-281">Don't write long blocks of manually-implemented <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic.</span></span> <span data-ttu-id="24a20-282">Preferovat soubory *. Razor* a umožněte kompilátoru, aby se zabývat pořadovým číslem.</span><span class="sxs-lookup"><span data-stu-id="24a20-282">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="24a20-283">Pokud se nemůžete vyhnout manuální <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logice, rozdělte dlouhé bloky kódu do menších částí zabalených v <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A> / <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> voláních.</span><span class="sxs-lookup"><span data-stu-id="24a20-283">If you're unable to avoid manual <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic, split long blocks of code into smaller pieces wrapped in <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenRegion%2A>/<xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseRegion%2A> calls.</span></span> <span data-ttu-id="24a20-284">Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).</span><span class="sxs-lookup"><span data-stu-id="24a20-284">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="24a20-285">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="24a20-285">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="24a20-286">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="24a20-286">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="24a20-287">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="24a20-287">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="24a20-288">používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="24a20-288"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="24a20-289">Rozdílování je mnohem rychlejší při použití pořadových čísel a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře, který vytváří soubory *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="24a20-289">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="perform-large-data-transfers-in-blazor-server-apps"></a><span data-ttu-id="24a20-290">Provádění rozsáhlých přenosů dat v Blazor serverových aplikacích</span><span class="sxs-lookup"><span data-stu-id="24a20-290">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="24a20-291">V některých scénářích je nutné přenášet velké objemy dat mezi jazykem JavaScript a Blazor .</span><span class="sxs-lookup"><span data-stu-id="24a20-291">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="24a20-292">K přenosu velkých objemů dat se obvykle dochází v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="24a20-292">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="24a20-293">Rozhraní API pro systém souborů v prohlížeči slouží k nahrání nebo stažení souboru.</span><span class="sxs-lookup"><span data-stu-id="24a20-293">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="24a20-294">Je vyžadována interoperabilita s knihovnou třetích stran.</span><span class="sxs-lookup"><span data-stu-id="24a20-294">Interop with a third party library is required.</span></span>

<span data-ttu-id="24a20-295">Na Blazor serveru platí omezení, aby nedocházelo k předávání jednoduchých zpráv, které by mohly způsobit problémy s výkonem.</span><span class="sxs-lookup"><span data-stu-id="24a20-295">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="24a20-296">Při vývoji kódu, který přenáší data mezi JavaScriptem a následujícími možnostmi, vezměte v úvahu následující pokyny Blazor :</span><span class="sxs-lookup"><span data-stu-id="24a20-296">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="24a20-297">Rozdělí data na menší části a datové segmenty se postupně odesílají, dokud server neobdrží všechna data.</span><span class="sxs-lookup"><span data-stu-id="24a20-297">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="24a20-298">Nepřiřazujte velké objekty v kódu JavaScript a C#.</span><span class="sxs-lookup"><span data-stu-id="24a20-298">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="24a20-299">Při odesílání nebo přijímání dat Neblokujte hlavní vlákno uživatelského rozhraní pro dlouhá období.</span><span class="sxs-lookup"><span data-stu-id="24a20-299">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="24a20-300">Uvolněte veškerou paměť, která je spotřebována při dokončení nebo zrušení procesu.</span><span class="sxs-lookup"><span data-stu-id="24a20-300">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="24a20-301">Vyjistěte následující další požadavky pro účely zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="24a20-301">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="24a20-302">Deklarujte maximální velikost souboru nebo dat, která se dají předat.</span><span class="sxs-lookup"><span data-stu-id="24a20-302">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="24a20-303">Deklarujte minimální rychlost nahrávání z klienta na server.</span><span class="sxs-lookup"><span data-stu-id="24a20-303">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="24a20-304">Po přijetí dat serverem můžou být tato data:</span><span class="sxs-lookup"><span data-stu-id="24a20-304">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="24a20-305">Dočasně uloženo do vyrovnávací paměti, dokud nebudou všechny segmenty shromažďovány.</span><span class="sxs-lookup"><span data-stu-id="24a20-305">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="24a20-306">Spotřebované hned.</span><span class="sxs-lookup"><span data-stu-id="24a20-306">Consumed immediately.</span></span> <span data-ttu-id="24a20-307">Data můžete například uložit hned do databáze nebo zapsat na disk při přijetí každého segmentu.</span><span class="sxs-lookup"><span data-stu-id="24a20-307">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="24a20-308">Následující třída pro odeslání souborů zpracovává zprostředkovatele komunikace JS s klientem.</span><span class="sxs-lookup"><span data-stu-id="24a20-308">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="24a20-309">Třída odeslání používá zprostředkovatele komunikace JS na:</span><span class="sxs-lookup"><span data-stu-id="24a20-309">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="24a20-310">Cyklické dotazování klienta o odeslání datového segmentu.</span><span class="sxs-lookup"><span data-stu-id="24a20-310">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="24a20-311">Zruší transakci, pokud vyprší časový limit pro cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="24a20-311">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime jsRuntime;
    private readonly int segmentSize = 6144;
    private readonly int maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> thisReference;
    private List<IMemoryOwner<byte>> uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        this.jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          uploadedSegments.Add(current);
        }

        var segments = uploadedSegments;
        uploadedSegments = null;

        return new SegmentedStream(segments, segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (uploadedSegments != null)
        {
            foreach (var segment in uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="24a20-312">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="24a20-312">In the preceding example:</span></span>

* <span data-ttu-id="24a20-313">`maxBase64SegmentSize`Je nastaven na `8192` , který je vypočten z `maxBase64SegmentSize = segmentSize * 4 / 3` .</span><span class="sxs-lookup"><span data-stu-id="24a20-313">The `maxBase64SegmentSize` is set to `8192`, which is calculated from `maxBase64SegmentSize = segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="24a20-314">Rozhraní API pro správu paměti nižší úrovně v rozhraní .NET Core se používají k ukládání segmentů paměti na serveru v `uploadedSegments` .</span><span class="sxs-lookup"><span data-stu-id="24a20-314">Low-level .NET Core memory management APIs are used to store the memory segments on the server in `uploadedSegments`.</span></span>
* <span data-ttu-id="24a20-315">`ReceiveFile`Metoda se používá ke zpracování odesílání prostřednictvím zprostředkovatele komunikace JS:</span><span class="sxs-lookup"><span data-stu-id="24a20-315">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="24a20-316">Velikost souboru je určena v bajtech přes interoperabilitu JS s `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` .</span><span class="sxs-lookup"><span data-stu-id="24a20-316">The file size is determined in bytes through JS interop with `jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="24a20-317">Počet segmentů, které se mají přijmout, se vypočítávají a ukládají v `numberOfSegments` .</span><span class="sxs-lookup"><span data-stu-id="24a20-317">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="24a20-318">Segmenty jsou požadovány ve `for` smyčce přes interoperabilitu js s `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` .</span><span class="sxs-lookup"><span data-stu-id="24a20-318">The segments are requested in a `for` loop through JS interop with `jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="24a20-319">Všechny segmenty, ale poslední, musí mít 8 192 bajtů před dekódováním.</span><span class="sxs-lookup"><span data-stu-id="24a20-319">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="24a20-320">Klient je nucen posílat data účinným způsobem.</span><span class="sxs-lookup"><span data-stu-id="24a20-320">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="24a20-321">Pro každý přijatý segment jsou kontroly provedeny před dekódováním pomocí <xref:System.Convert.TryFromBase64String%2A> .</span><span class="sxs-lookup"><span data-stu-id="24a20-321">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String%2A>.</span></span>
  * <span data-ttu-id="24a20-322">Po dokončení nahrávání se datový proud s daty vrátí jako nový <xref:System.IO.Stream> ( `SegmentedStream` ).</span><span class="sxs-lookup"><span data-stu-id="24a20-322">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="24a20-323">Třída segmentace Stream zpřístupňuje seznam segmentů jako jen pro čtení, které nemůžete prohledávat <xref:System.IO.Stream> :</span><span class="sxs-lookup"><span data-stu-id="24a20-323">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> sequence;
    private long currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            sequence = new ReadOnlySequence<byte>(
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

        sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(currentPosition + count < sequence.Length ? 
            count : sequence.Length - currentPosition);
        var data = sequence.Slice(currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        currentPosition += bytesToWrite;

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

<span data-ttu-id="24a20-324">Následující kód implementuje funkce JavaScriptu pro příjem dat:</span><span class="sxs-lookup"><span data-stu-id="24a20-324">The following code implements JavaScript functions to receive the data:</span></span>

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
