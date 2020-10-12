---
title: Blazor WebAssemblyOsvědčené postupy týkající se ASP.NET Core výkonu
author: pranavkm
description: Tipy pro zvýšení výkonu v Blazor WebAssembly aplikacích ASP.NET Core a předcházení běžným problémům s výkonem.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: ea3f197e5bab82f4fb40238fe31cd5ce29ab62ad
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900970"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="800f5-103">Blazor WebAssemblyOsvědčené postupy týkající se ASP.NET Core výkonu</span><span class="sxs-lookup"><span data-stu-id="800f5-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="800f5-104">Od [Pranav Krishnamoorthy](https://github.com/pranavkm) a [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="800f5-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="800f5-105">Blazor WebAssembly je pečlivě navržená a optimalizovaná tak, aby umožňovala vysoký výkon ve většině reálných scénářů uživatelského rozhraní aplikací.</span><span class="sxs-lookup"><span data-stu-id="800f5-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="800f5-106">Vyprodukování nejlepších výsledků však závisí na vývojářích, kteří používají správné vzory a funkce.</span><span class="sxs-lookup"><span data-stu-id="800f5-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="800f5-107">Vezměte v úvahu následující aspekty:</span><span class="sxs-lookup"><span data-stu-id="800f5-107">Consider the following aspects:</span></span>

* <span data-ttu-id="800f5-108">**Běhová propustnost**: kód .NET běží na Překladači v rámci modulu WebAssembly runtime, takže propustnost procesoru je omezená.</span><span class="sxs-lookup"><span data-stu-id="800f5-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="800f5-109">V případě náročných scénářů aplikace těží z [Optimalizace rychlosti vykreslování](#optimize-rendering-speed).</span><span class="sxs-lookup"><span data-stu-id="800f5-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="800f5-110">**Čas spuštění**: aplikace přenáší modul runtime .NET do prohlížeče, takže je důležité používat funkce, které [minimalizují velikost stahovaných aplikací](#minimize-app-download-size).</span><span class="sxs-lookup"><span data-stu-id="800f5-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="800f5-111">Optimalizovat rychlost vykreslování</span><span class="sxs-lookup"><span data-stu-id="800f5-111">Optimize rendering speed</span></span>

<span data-ttu-id="800f5-112">V následujících oddílech jsou uvedeny doporučení pro minimalizaci úlohy vykreslování a zlepšení rychlosti odezvy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="800f5-113">Podle těchto pokynů můžete snadno vytvořit *10 nebo vyšší vylepšení* rychlosti vykreslování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="800f5-114">Vyhnout se zbytečnému vykreslování podstromů komponent</span><span class="sxs-lookup"><span data-stu-id="800f5-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="800f5-115">Za běhu komponenty existují jako hierarchie.</span><span class="sxs-lookup"><span data-stu-id="800f5-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="800f5-116">Kořenová komponenta má podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="800f5-116">A root component has child components.</span></span> <span data-ttu-id="800f5-117">Pak mají podřízené položky kořenového adresáře vlastní podřízené komponenty a tak dále.</span><span class="sxs-lookup"><span data-stu-id="800f5-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="800f5-118">V případě, že dojde k události, jako je například uživatel, který vybírá tlačítko, se jedná o způsob, jakým se budou Blazor součásti znovu vykreslovat:</span><span class="sxs-lookup"><span data-stu-id="800f5-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

 1. <span data-ttu-id="800f5-119">Událost samotná je odeslána do jakékoli součásti vygenerované obslužnou rutinou události.</span><span class="sxs-lookup"><span data-stu-id="800f5-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="800f5-120">Po spuštění obslužné rutiny události se tato součást znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="800f5-120">After executing the event handler, that component is rerendered.</span></span>
 1. <span data-ttu-id="800f5-121">Pokaždé, když je znovu vykreslena nějaká komponenta, poskytuje novou kopii hodnot parametrů každé z jejích podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="800f5-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
 1. <span data-ttu-id="800f5-122">Při přijímání nové sady hodnot parametrů každá součást zvolí, zda se má znovu vykreslit.</span><span class="sxs-lookup"><span data-stu-id="800f5-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="800f5-123">Ve výchozím nastavení se komponenty znovu vykreslují, pokud se hodnoty parametrů mohly změnit (například pokud jsou proměnlivé objekty).</span><span class="sxs-lookup"><span data-stu-id="800f5-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="800f5-124">Poslední dva kroky této sekvence budou rekurzivně vycházet z hierarchie součástí.</span><span class="sxs-lookup"><span data-stu-id="800f5-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="800f5-125">V mnoha případech se celý podstrom znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="800f5-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="800f5-126">To znamená, že události, které cílí na komponenty vysoké úrovně, mohou způsobit náročné procesy opětovného vykreslování, protože vše pod tímto bodem musí být znovu vykresleno.</span><span class="sxs-lookup"><span data-stu-id="800f5-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="800f5-127">Pokud chcete tento proces přerušit a zabránit vykreslování rekurze do konkrétního podstromu, můžete buď:</span><span class="sxs-lookup"><span data-stu-id="800f5-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

 * <span data-ttu-id="800f5-128">Zajistěte, aby všechny parametry určité součásti byly primitivních neměnných typů (například,,, `string` `int` `bool` `DateTime` a další).</span><span class="sxs-lookup"><span data-stu-id="800f5-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="800f5-129">Vestavěná logika pro zjišťování změn automaticky přeskočí opětovné vykreslení, pokud žádná z těchto hodnot parametrů nebyla změněna.</span><span class="sxs-lookup"><span data-stu-id="800f5-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="800f5-130">Pokud vykreslíte podřízenou komponentu pomocí `<Customer CustomerId="@item.CustomerId" />` , kde `CustomerId` je `int` hodnota, pak není znovu vykreslena s výjimkou `item.CustomerId` změn.</span><span class="sxs-lookup"><span data-stu-id="800f5-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
 * <span data-ttu-id="800f5-131">Pokud potřebujete přijmout neprimitivní hodnoty parametrů, jako jsou například vlastní typy modelů, zpětná volání událostí nebo <xref:Microsoft.AspNetCore.Components.RenderFragment> hodnoty, můžete přepsat <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> pro řízení rozhodování o tom, zda se má vykreslit, což je popsáno v části [ `ShouldRender` použití](#use-of-shouldrender) oddílu.</span><span class="sxs-lookup"><span data-stu-id="800f5-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="800f5-132">Vynecháním revykreslování celých podstromů může být možné odebrat převážnou většinu nákladů na vykreslování, když dojde k události.</span><span class="sxs-lookup"><span data-stu-id="800f5-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="800f5-133">Můžete chtít, aby podřízené komponenty byly vytvořeny konkrétně, abyste mohli přeskočit znovu vykreslování, které je součástí uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="800f5-134">Toto je platný způsob, jak snížit náklady na vykreslování nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="800f5-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="800f5-135">Použití `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="800f5-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="800f5-136">Pokud vytváříte komponentu pouze s uživatelským rozhraním, která se po počátečním vykreslení nemění (bez ohledu na hodnoty parametrů), nakonfigurujte, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> aby vracela `false` :</span><span class="sxs-lookup"><span data-stu-id="800f5-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="800f5-137">Pokud komponenta vyžaduje převykreslování pouze v případě, že hodnoty parametru jsou vhodné pro konkrétní způsob, můžete použít soukromá pole ke sledování potřebných informací pro detekci změn.</span><span class="sxs-lookup"><span data-stu-id="800f5-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="800f5-138">V následujícím příkladu `shouldRender` je založena na kontrole jakéhokoli druhu změny nebo mutace, které by měly zobrazit výzvu k revykreslování.</span><span class="sxs-lookup"><span data-stu-id="800f5-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="800f5-139">`prevOutboundFlightId` a `prevInboundFlightId` Sledujte informace o další možné aktualizaci:</span><span class="sxs-lookup"><span data-stu-id="800f5-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="800f5-140">V předchozím kódu může obslužná rutina události také nastavit `shouldRender` `true` tak, aby se komponenta po události znovu vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="800f5-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="800f5-141">Pro většinu komponent není tato úroveň ručního řízení nutná.</span><span class="sxs-lookup"><span data-stu-id="800f5-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="800f5-142">Měli byste se zabývat pouze přeskočením podstromů vykreslování, pokud jsou tyto podstromy zvláště nákladné pro vykreslování a způsobují prodlevu uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="800f5-143">Další informace naleznete v tématu <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="800f5-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="800f5-144">Virtualizace</span><span class="sxs-lookup"><span data-stu-id="800f5-144">Virtualization</span></span>

<span data-ttu-id="800f5-145">Při vykreslování velkých objemů uživatelského rozhraní v rámci smyčky, například seznamu nebo mřížky s tisíci položkami, může Sheer množství operací vykreslování vést k prodlevě při vykreslování uživatelského rozhraní a tudíž k nedostatečnému uživatelskému prostředí.</span><span class="sxs-lookup"><span data-stu-id="800f5-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="800f5-146">Vzhledem k tomu, že uživatel může zobrazit pouze malý počet prvků současně bez posouvání, zdá se, že wasteful stráví spoustu času vykreslováním prvků, které nejsou aktuálně viditelné.</span><span class="sxs-lookup"><span data-stu-id="800f5-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="800f5-147">K tomuto řešení Blazor poskytuje vestavěnou [ `<Virtualize>` komponentu](xref:blazor/components/virtualization) , která vytvoří chování při zobrazení a posouvání v libovolně velkém seznamu, ale ve skutečnosti vykreslí pouze položky seznamu, které jsou v aktuálním zobrazení posuvníku.</span><span class="sxs-lookup"><span data-stu-id="800f5-147">To address this, Blazor provides a built-in [`<Virtualize>` component](xref:blazor/components/virtualization) that creates the appearance and scroll behaviors of an arbitrarily-large list but actually only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="800f5-148">To například znamená, že aplikace může obsahovat seznam s 100 000 položkami, ale pouze platíte náklady na vykreslování na 20 položek, které jsou zobrazeny v jednom okamžiku.</span><span class="sxs-lookup"><span data-stu-id="800f5-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="800f5-149">Použití `<Virtualize>` komponenty může škálovat výkon uživatelského rozhraní podle pořadí podle velikosti.</span><span class="sxs-lookup"><span data-stu-id="800f5-149">Use of the `<Virtualize>` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="800f5-150">`<Virtualize>` dá se použít, když:</span><span class="sxs-lookup"><span data-stu-id="800f5-150">`<Virtualize>` can be used when:</span></span>

 * <span data-ttu-id="800f5-151">Vykreslení sady datových položek ve smyčce.</span><span class="sxs-lookup"><span data-stu-id="800f5-151">Rendering a set of data items in a loop.</span></span>
 * <span data-ttu-id="800f5-152">Většina položek není viditelná v důsledku posouvání.</span><span class="sxs-lookup"><span data-stu-id="800f5-152">Most of the items aren't visible due to scrolling.</span></span>
 * <span data-ttu-id="800f5-153">Vykreslené položky mají přesně stejnou velikost.</span><span class="sxs-lookup"><span data-stu-id="800f5-153">The rendered items are exactly the same size.</span></span> <span data-ttu-id="800f5-154">Když se uživatel posune na libovolný bod, komponenta může vypočítat viditelné položky k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="800f5-154">When the user scrolls to an arbitrary point, the component can calculate the visible items to show.</span></span>

<span data-ttu-id="800f5-155">Následující příklad ukazuje příklad nevirtualizovaného seznamu:</span><span class="sxs-lookup"><span data-stu-id="800f5-155">The following shows an example of a non-virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    @foreach (var flight in allFlights)
    {
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    }
</div>
```

<span data-ttu-id="800f5-156">Pokud `allFlights` kolekce obsahuje 10 000 položek, vytvoří instance a vykresluje 10 000 `<FlightSummary>` instancí komponent.</span><span class="sxs-lookup"><span data-stu-id="800f5-156">If the `allFlights` collection holds 10,000 items, it instantiates and renders 10,000 `<FlightSummary>` component instances.</span></span> <span data-ttu-id="800f5-157">V porovnání ukazuje následující příklad virtualizovaného seznamu:</span><span class="sxs-lookup"><span data-stu-id="800f5-157">In comparison, the following shows an example of a virtualized list:</span></span>

```razor
<div class="all-flights" style="height:500px;overflow-y:scroll">
    <Virtualize Items="@allFlights" Context="flight">
        <FlightSummary @key="flight.FlightId" Flight="@flight" />
    </Virtualize>
</div>
```

<span data-ttu-id="800f5-158">I když výsledné uživatelské rozhraní vypadá stejně pro uživatele, na pozadí se komponenta pouze vytváří a vykresluje jako mnoho `<FlightSummary>` instancí, kolik je potřeba k vyplnění rolovací oblasti.</span><span class="sxs-lookup"><span data-stu-id="800f5-158">Even though the resulting UI looks the same to a user, behind the scenes the component only instantiates and renders as many `<FlightSummary>` instances as are required to fill the scrollable region.</span></span> <span data-ttu-id="800f5-159">Sada `<FlightSummary>` zobrazených instancí se přepočítá a vykreslí při posunu uživatele.</span><span class="sxs-lookup"><span data-stu-id="800f5-159">The set of `<FlightSummary>` instances displayed is recalculated and rendered as the user scrolls.</span></span>

<span data-ttu-id="800f5-160">`<Virtualize>` má také jiné výhody.</span><span class="sxs-lookup"><span data-stu-id="800f5-160">`<Virtualize>` has other benefits, too.</span></span> <span data-ttu-id="800f5-161">Například pokud komponenta požaduje data z externího rozhraní API, `<Virtualize>` umožňuje komponentě načíst jenom řez záznamů, které odpovídají aktuální viditelné oblasti, místo aby se stáhla všechna data z kolekce.</span><span class="sxs-lookup"><span data-stu-id="800f5-161">For example when a component requests data from an external API, `<Virtualize>` permits the component to only fetch the slice of records that correspond to the current visible region, instead of downloading all the data from the collection.</span></span>

<span data-ttu-id="800f5-162">Další informace naleznete v tématu <xref:blazor/components/virtualization>.</span><span class="sxs-lookup"><span data-stu-id="800f5-162">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="800f5-163">Vytváření odlehčených optimalizovaných komponent</span><span class="sxs-lookup"><span data-stu-id="800f5-163">Create lightweight, optimized components</span></span>

<span data-ttu-id="800f5-164">Většina Blazor součástí nevyžaduje agresivní optimalizační úsilí.</span><span class="sxs-lookup"><span data-stu-id="800f5-164">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="800f5-165">Důvodem je to, že většina komponent se v uživatelském rozhraní často neopakuje a neprovádí se znovu s vysokou frekvencí.</span><span class="sxs-lookup"><span data-stu-id="800f5-165">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="800f5-166">Například `@page` komponenty a komponenty představující uživatelské rozhraní vysoké úrovně, jako jsou dialogová okna nebo formuláře, se pravděpodobně zobrazují pouze po jednom a znovu se vykreslují v reakci na gesto uživatele.</span><span class="sxs-lookup"><span data-stu-id="800f5-166">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="800f5-167">Tyto komponenty nevytvářejí vysokou úlohu vykreslování, takže můžete volně používat libovolnou kombinaci funkcí rozhraní, které chcete, aniž byste se museli starat o výkon vykreslování.</span><span class="sxs-lookup"><span data-stu-id="800f5-167">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="800f5-168">Existují však i běžné scénáře, kde sestavíte součásti, které je třeba opakovaně škálovat.</span><span class="sxs-lookup"><span data-stu-id="800f5-168">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="800f5-169">Například:</span><span class="sxs-lookup"><span data-stu-id="800f5-169">For example:</span></span>

 * <span data-ttu-id="800f5-170">Velké vnořené formuláře mohou obsahovat stovky individuálních vstupů, popisků a dalších prvků.</span><span class="sxs-lookup"><span data-stu-id="800f5-170">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
 * <span data-ttu-id="800f5-171">Mřížky mohou obsahovat tisíce buněk.</span><span class="sxs-lookup"><span data-stu-id="800f5-171">Grids may have thousands of cells.</span></span>
 * <span data-ttu-id="800f5-172">Bodový graf může mít miliony datových bodů.</span><span class="sxs-lookup"><span data-stu-id="800f5-172">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="800f5-173">Pokud se každá jednotka řadí jako samostatné instance komponenty, bude to mnoho z nich, aby jejich výkon při vykreslování byl kritický.</span><span class="sxs-lookup"><span data-stu-id="800f5-173">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="800f5-174">V této části najdete pokyny k tomu, aby tyto komponenty byly odlehčené, takže uživatelské rozhraní zůstane rychlé a reagovat.</span><span class="sxs-lookup"><span data-stu-id="800f5-174">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="800f5-175">Vyhněte se tisícům instancí součástí</span><span class="sxs-lookup"><span data-stu-id="800f5-175">Avoid thousands of component instances</span></span>

<span data-ttu-id="800f5-176">Každá součást je samostatný ostrov, který lze vykreslit nezávisle na svých nadřazených a podřízených objektech.</span><span class="sxs-lookup"><span data-stu-id="800f5-176">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="800f5-177">Výběrem způsobu rozdělení uživatelského rozhraní do hierarchie komponent převezmete kontrolu nad členitost vykreslování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-177">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="800f5-178">Může to být pro výkon buď dobrá, nebo nesprávná.</span><span class="sxs-lookup"><span data-stu-id="800f5-178">This can be either good or bad for performance.</span></span>

 * <span data-ttu-id="800f5-179">Rozdělením uživatelského rozhraní do více komponent můžete mít menší části uživatelského rozhraní znovu vykreslit, když dojde k událostem.</span><span class="sxs-lookup"><span data-stu-id="800f5-179">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="800f5-180">Například když uživatel klikne na tlačítko v řádku tabulky, bude možné mít pouze jeden řádek znovu vykreslit místo celé stránky nebo tabulky.</span><span class="sxs-lookup"><span data-stu-id="800f5-180">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
 * <span data-ttu-id="800f5-181">Každá další součást ale zahrnuje určitou dodatečnou paměť a režii procesoru při zakládání se svým nezávislým stavem a životním cyklem vykreslování.</span><span class="sxs-lookup"><span data-stu-id="800f5-181">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="800f5-182">Při vyladění výkonu Blazor WebAssembly rozhraní .NET 5 jsme zjistili režii vykreslování přibližně 0,06 MS za instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="800f5-182">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="800f5-183">Vychází z jednoduché součásti, která přijímá tři parametry běžící na typickém přenosném počítači.</span><span class="sxs-lookup"><span data-stu-id="800f5-183">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="800f5-184">Interní režie je z velké části z důvodu načítání stavu jednotlivých součástí ze slovníků a předávání a přijímání parametrů.</span><span class="sxs-lookup"><span data-stu-id="800f5-184">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="800f5-185">Pomocí násobení můžete vidět, že přidání 2 000 instancí součástí by do doby vykreslování přidali 0,12 sekund a uživatelské rozhraní by mohlo zpomalit uživatele.</span><span class="sxs-lookup"><span data-stu-id="800f5-185">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="800f5-186">Je možné, aby komponenty byly mnohem obtížnější, abyste je mohli využít, ale často výkonnější techniky nemají tolik komponent.</span><span class="sxs-lookup"><span data-stu-id="800f5-186">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="800f5-187">Následující části popisují dva přístupy.</span><span class="sxs-lookup"><span data-stu-id="800f5-187">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="800f5-188">Vložené podřízené komponenty do jejich nadřazených prvků</span><span class="sxs-lookup"><span data-stu-id="800f5-188">Inline child components into their parents</span></span>

<span data-ttu-id="800f5-189">Vezměte v úvahu následující komponentu, která vykreslí sekvenci podřízených komponent:</span><span class="sxs-lookup"><span data-stu-id="800f5-189">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="800f5-190">Pro předchozí příklad kódu `<ChatMessageDisplay>` je komponenta definována v souboru `ChatMessageDisplay.razor` obsahujícím:</span><span class="sxs-lookup"><span data-stu-id="800f5-190">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="800f5-191">Předchozí příklad funguje správně a má za předpokladu, že tisíce zpráv nejsou zobrazeny najednou.</span><span class="sxs-lookup"><span data-stu-id="800f5-191">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="800f5-192">Chcete-li zobrazit tisíce zpráv najednou, zvažte, jestli *nechcete* oddělit jednotlivé `ChatMessageDisplay` součásti.</span><span class="sxs-lookup"><span data-stu-id="800f5-192">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="800f5-193">Místo toho je třeba vložit vykreslování přímo do nadřazeného objektu:</span><span class="sxs-lookup"><span data-stu-id="800f5-193">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="800f5-194">Tím se zabrání režie na součást vykreslování, takže mnoho podřízených komponent za cenu není schopné je nezávisle znovu vykreslovat.</span><span class="sxs-lookup"><span data-stu-id="800f5-194">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="800f5-195">Definovat opakované použití `RenderFragments` v kódu</span><span class="sxs-lookup"><span data-stu-id="800f5-195">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="800f5-196">Podřízené komponenty můžete vyfiltrovat čistě jako způsob, jak znovu použít logiku vykreslování.</span><span class="sxs-lookup"><span data-stu-id="800f5-196">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="800f5-197">Pokud je to tento případ, je stále možné znovu použít logiku vykreslování bez deklarování skutečných komponent.</span><span class="sxs-lookup"><span data-stu-id="800f5-197">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="800f5-198">V bloku jakékoli komponenty `@code` můžete definovat <xref:Microsoft.AspNetCore.Components.RenderFragment> , který EMITUJE uživatelské rozhraní a který lze volat odkudkoli:</span><span class="sxs-lookup"><span data-stu-id="800f5-198">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="800f5-199">Jak je demonstated v předchozím příkladu, komponenty mohou generovat značky z kódu v rámci svého `@code` bloku a mimo něj.</span><span class="sxs-lookup"><span data-stu-id="800f5-199">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="800f5-200">Tento přístup definuje <xref:Microsoft.AspNetCore.Components.RenderFragment> delegáta, který můžete vykreslit uvnitř normálního výstupu vykreslování komponenty, volitelně na více místech.</span><span class="sxs-lookup"><span data-stu-id="800f5-200">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="800f5-201">Je nezbytné, aby mohl delegát přijmout parametr s názvem `__builder` <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> , aby Razor kompilátor mohl vytvořit pokyny pro vykreslování pro tento typ.</span><span class="sxs-lookup"><span data-stu-id="800f5-201">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="800f5-202">Pokud chcete tuto možnost opakovaně použít napříč více komponentami, zvažte její deklaraci jako `public static` člena:</span><span class="sxs-lookup"><span data-stu-id="800f5-202">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="800f5-203">Tuto chybu lze nyní vyvolat z nesouvisející součásti.</span><span class="sxs-lookup"><span data-stu-id="800f5-203">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="800f5-204">Tato technika je užitečná pro vytváření knihoven opakovaně použitelných fragmentů kódu, které vykreslují bez režie pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="800f5-204">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="800f5-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> Delegáti mohou také přijímat parametry.</span><span class="sxs-lookup"><span data-stu-id="800f5-205"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="800f5-206">Chcete-li vytvořit ekvivalent `ChatMessageDisplay` komponenty z předchozího příkladu:</span><span class="sxs-lookup"><span data-stu-id="800f5-206">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @DisplayChatMessage(message)
    }
</div>

@code {
    RenderFragment<ChatMessage> DisplayChatMessage = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="800f5-207">Tento přístup poskytuje výhodu opětovného použití logiky vykreslování bez režie pro jednotlivé komponenty.</span><span class="sxs-lookup"><span data-stu-id="800f5-207">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="800f5-208">Nemá ale výhodu pro možnost aktualizovat svůj podstrom uživatelského rozhraní nezávisle, ani nemůže přeskočit vykreslování, který podstrom uživatelského rozhraní při vykreslení jeho nadřazeného objektu, protože neexistuje hranice součásti.</span><span class="sxs-lookup"><span data-stu-id="800f5-208">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="800f5-209">Nedostávat příliš mnoho parametrů</span><span class="sxs-lookup"><span data-stu-id="800f5-209">Don't receive too many parameters</span></span>

<span data-ttu-id="800f5-210">Pokud se komponenta opakuje velmi často, například stovky nebo tisíce časů, pak je potřeba mít na paměti, že režie při předávání a přijímání jednotlivých parametrů.</span><span class="sxs-lookup"><span data-stu-id="800f5-210">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="800f5-211">Je málo pravděpodobné, že příliš mnoho parametrů vážně omezuje výkon, ale může se jednat o faktor.</span><span class="sxs-lookup"><span data-stu-id="800f5-211">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="800f5-212">Pro `<TableCell>` komponentu, která vykresluje v rámci mřížky 1 000 časů, každý další předaný parametr by mohl přidat přibližně 15 MS na celkové náklady na vykreslení.</span><span class="sxs-lookup"><span data-stu-id="800f5-212">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="800f5-213">Pokud každá buňka přijala 10 parametrů, předávání parametru přebírá přibližně 150 MS na vykreslování komponenty a tedy asi 150 000 ms (150 sekund) a na vlastní příčinu neurčitého uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-213">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="800f5-214">Pro snížení tohoto zatížení můžete seskupit více parametrů prostřednictvím vlastních tříd.</span><span class="sxs-lookup"><span data-stu-id="800f5-214">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="800f5-215">`<TableCell>`Komponenta může například přijmout:</span><span class="sxs-lookup"><span data-stu-id="800f5-215">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="800f5-216">V předchozím příkladu je aplikace `Data` pro každou buňku odlišná, ale `Options` je společná napříč všemi nimi.</span><span class="sxs-lookup"><span data-stu-id="800f5-216">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="800f5-217">Samozřejmě se může jednat o vylepšení, které neobsahují `<TableCell>` komponentu a místo toho by měla být vložena její logika do nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="800f5-217">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="800f5-218">Zajistěte, aby se kaskádové parametry opravily</span><span class="sxs-lookup"><span data-stu-id="800f5-218">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="800f5-219">`<CascadingValue>`Komponenta má volitelný parametr s názvem `IsFixed` .</span><span class="sxs-lookup"><span data-stu-id="800f5-219">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

 * <span data-ttu-id="800f5-220">Pokud `IsFixed` je hodnota `false` (výchozí nastavení), pak každý příjemce v kaskádové hodnotě nastaví předplatné pro přijímání oznámení o změně.</span><span class="sxs-lookup"><span data-stu-id="800f5-220">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="800f5-221">V takovém případě je každá `[CascadingParameter]` z nich z důvodu sledování předplatného **podstatně dražší** , než je obvyklé `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="800f5-221">In this case, each each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
 * <span data-ttu-id="800f5-222">Pokud `IsFixed` je hodnota `true` (například `<CascadingValue Value="@someValue" IsFixed="true">` ), pak příjemci obdrží počáteční hodnotu, ale nenastaví žádné předplatné *not* pro příjem aktualizací.</span><span class="sxs-lookup"><span data-stu-id="800f5-222">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="800f5-223">V takovém případě je každá z nich `[CascadingParameter]` odlehčená a **nesmí být dražší** než pravidelná `[Parameter]` .</span><span class="sxs-lookup"><span data-stu-id="800f5-223">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="800f5-224">Takže pokud je to možné, měli byste použít `IsFixed="true"` na kaskádové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="800f5-224">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="800f5-225">To můžete provést vždy, když se zadaná hodnota nemění v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="800f5-225">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="800f5-226">V běžném vzoru, kde součást přechází `this` jako na kaskádovou hodnotu, byste měli použít `IsFixed="true"` :</span><span class="sxs-lookup"><span data-stu-id="800f5-226">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="800f5-227">To přináší velký rozdíl v případě, že existuje velký počet dalších komponent, které získají kaskádovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="800f5-227">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="800f5-228">Další informace naleznete v tématu <xref:blazor/components/cascading-values-and-parameters>.</span><span class="sxs-lookup"><span data-stu-id="800f5-228">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="800f5-229">Vyhnout se atributu seskupováním s `CaptureUnmatchedValues`</span><span class="sxs-lookup"><span data-stu-id="800f5-229">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="800f5-230">Komponenty se mohou rozhodnout, že budou přijímat "nespárované" hodnoty parametrů pomocí <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> příznaku:</span><span class="sxs-lookup"><span data-stu-id="800f5-230">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="800f5-231">Tento přístup umožňuje předávání libovolných dalších atributů elementu.</span><span class="sxs-lookup"><span data-stu-id="800f5-231">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="800f5-232">Je však také poměrně nákladné, protože zobrazovací jednotka musí:</span><span class="sxs-lookup"><span data-stu-id="800f5-232">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="800f5-233">Porovná všechny zadané parametry se sadou známých parametrů pro sestavení slovníku.</span><span class="sxs-lookup"><span data-stu-id="800f5-233">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="800f5-234">Udržujte si přehled o tom, jak různé kopie stejného atributu jsou vzájemně přepsány.</span><span class="sxs-lookup"><span data-stu-id="800f5-234">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="800f5-235">Používejte <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> nepostradatelné komponenty, jako jsou ty, které se neopakují často.</span><span class="sxs-lookup"><span data-stu-id="800f5-235">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="800f5-236">Nicméně pro komponenty, které jsou škálovatelné, například jednotlivé položky v rozsáhlých seznamech nebo buňkách v mřížce, se pokuste vyhnout atributu seskupováním.</span><span class="sxs-lookup"><span data-stu-id="800f5-236">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="800f5-237">Další informace naleznete v tématu <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="800f5-237">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="800f5-238">`SetParametersAsync`Ruční implementace</span><span class="sxs-lookup"><span data-stu-id="800f5-238">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="800f5-239">Jednou z hlavních aspektů režie vykreslování jednotlivých komponent je zápis hodnot příchozích parametrů do `[Parameter]` vlastností.</span><span class="sxs-lookup"><span data-stu-id="800f5-239">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="800f5-240">K tomuto účelu musí zobrazovací jednotka použít reflexi.</span><span class="sxs-lookup"><span data-stu-id="800f5-240">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="800f5-241">I když je to poněkud optimalizované, neexistence podpory JIT v modulu runtime WebAssembly ukládá omezení.</span><span class="sxs-lookup"><span data-stu-id="800f5-241">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="800f5-242">V některých extrémních případech možná budete chtít zabránit reflexi a implementovat logiku vlastního nastavení parametrů ručně.</span><span class="sxs-lookup"><span data-stu-id="800f5-242">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="800f5-243">To může být použitelné v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="800f5-243">This may be applicable when:</span></span>

 * <span data-ttu-id="800f5-244">Máte komponentu, která vykresluje extrémně často (například existují stovky nebo tisíce kopií v uživatelském rozhraní).</span><span class="sxs-lookup"><span data-stu-id="800f5-244">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
 * <span data-ttu-id="800f5-245">Přijímá mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="800f5-245">It accepts many parameters.</span></span>
 * <span data-ttu-id="800f5-246">Zjistíte, že režie přijímání parametrů má pozorovatelný vliv na odezvu uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-246">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="800f5-247">V těchto případech můžete přepsat virtuální <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> metodu komponenty a implementovat vlastní logiku specifickou pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="800f5-247">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="800f5-248">Následující příklad záměrně zabrání žádnému vyhledávání slovníku:</span><span class="sxs-lookup"><span data-stu-id="800f5-248">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="800f5-249">V předchozím kódu návrat základní třídy <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> spouští normální metody životního cyklu bez přiřazení parametrů znovu.</span><span class="sxs-lookup"><span data-stu-id="800f5-249">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="800f5-250">Jak vidíte v předchozím kódu, přepsání <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> a poskytnutí vlastní logiky jsou komplikované a pracné, takže tento přístup nedoporučujeme obecně.</span><span class="sxs-lookup"><span data-stu-id="800f5-250">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="800f5-251">V extrémních případech může zvýšit výkon při vykreslování 20-25%, ale měli byste zvážit jenom tento postup ve výše uvedených scénářích.</span><span class="sxs-lookup"><span data-stu-id="800f5-251">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="800f5-252">Nespouštět události příliš rychle</span><span class="sxs-lookup"><span data-stu-id="800f5-252">Don't trigger events too rapidly</span></span>

<span data-ttu-id="800f5-253">Některé události prohlížeče se proaktivují extrémně často, například `onmousemove` a `onscroll` , což může aktivovat desítky nebo stovky časů za sekundu.</span><span class="sxs-lookup"><span data-stu-id="800f5-253">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="800f5-254">Ve většině případů nemusíte často provádět aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="800f5-254">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="800f5-255">Pokud se o to pokusíte, můžete poškodit rychlost odezvy uživatelského rozhraní nebo spotřebovávat nadměrný čas procesoru.</span><span class="sxs-lookup"><span data-stu-id="800f5-255">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="800f5-256">Namísto použití nativních `@onmousemove` nebo `@onscroll` událostí můžete chtít použít zprostředkovatele komunikace js k registraci zpětného volání, které se aktivuje méně často.</span><span class="sxs-lookup"><span data-stu-id="800f5-256">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="800f5-257">Například následující součást ( `MyComponent.razor` ) zobrazuje pozici myši, ale pouze aktualizace vždy každých 500 ms:</span><span class="sxs-lookup"><span data-stu-id="800f5-257">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="800f5-258">Odpovídající kód JavaScriptu, který lze umístit na `index.html` stránku nebo načíst jako modul ES6, zaregistruje skutečný naslouchací proces událostí DOM.</span><span class="sxs-lookup"><span data-stu-id="800f5-258">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="800f5-259">V tomto příkladu naslouchací proces událostí používá [ `throttle` funkci Lodash](https://lodash.com/docs/4.17.15#throttle) k omezení míry vyvolání:</span><span class="sxs-lookup"><span data-stu-id="800f5-259">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="800f5-260">Tato technika může být ještě důležitější pro Blazor Server , protože každé vyvolání události zahrnuje doručení zprávy přes síť.</span><span class="sxs-lookup"><span data-stu-id="800f5-260">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="800f5-261">To je užitečné, Blazor WebAssembly protože může významně snížit množství práce při vykreslování.</span><span class="sxs-lookup"><span data-stu-id="800f5-261">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="800f5-262">Optimalizovat rychlost komunikace JavaScript</span><span class="sxs-lookup"><span data-stu-id="800f5-262">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="800f5-263">Volání mezi .NET a JavaScript se skládá z nějaké další režie, protože:</span><span class="sxs-lookup"><span data-stu-id="800f5-263">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

 * <span data-ttu-id="800f5-264">Ve výchozím nastavení jsou volání asynchronní.</span><span class="sxs-lookup"><span data-stu-id="800f5-264">By default, calls are asynchronous.</span></span>
 * <span data-ttu-id="800f5-265">Ve výchozím nastavení jsou parametry a návratové hodnoty serializovány pomocí formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="800f5-265">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="800f5-266">Slouží k poskytnutí snadno pochopitelného mechanismu převodů mezi typy rozhraní .NET a JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="800f5-266">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="800f5-267">Kromě toho Blazor Server se tato volání předávají přes síť.</span><span class="sxs-lookup"><span data-stu-id="800f5-267">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="800f5-268">Vyhněte se nadměrnému vyjemnému volání</span><span class="sxs-lookup"><span data-stu-id="800f5-268">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="800f5-269">Vzhledem k tomu, že každé volání zahrnuje určitou režii, může být užitečné snížit počet volání.</span><span class="sxs-lookup"><span data-stu-id="800f5-269">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="800f5-270">Vezměte v úvahu následující kód, který ukládá kolekci položek v `localStorage` úložišti prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="800f5-270">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="800f5-271">Předchozí příklad provede samostatné volání interoperability JS pro každou položku.</span><span class="sxs-lookup"><span data-stu-id="800f5-271">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="800f5-272">Místo toho následující přístup snižuje zprostředkovatele komunikace JS na jedno volání:</span><span class="sxs-lookup"><span data-stu-id="800f5-272">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="800f5-273">Odpovídající funkce jazyka JavaScript definovaná následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="800f5-273">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="800f5-274">K Blazor WebAssembly tomu obvykle dochází pouze v případě, že provádíte velké množství volání interoperability js.</span><span class="sxs-lookup"><span data-stu-id="800f5-274">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="800f5-275">Zvažte provedení synchronních volání.</span><span class="sxs-lookup"><span data-stu-id="800f5-275">Consider making synchronous calls</span></span>

<span data-ttu-id="800f5-276">Volání interoperability JavaScriptu jsou ve výchozím nastavení asynchronní, bez ohledu na to, zda je kód volán synchronní nebo asynchronní.</span><span class="sxs-lookup"><span data-stu-id="800f5-276">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="800f5-277">K tomu je potřeba zajistit, aby komponenty byly kompatibilní s Blazor WebAssembly a Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="800f5-277">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="800f5-278">V systému Blazor Server musí být všechna volání interoperability JavaScriptu asynchronní, protože jsou odesílána přes síťové připojení.</span><span class="sxs-lookup"><span data-stu-id="800f5-278">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="800f5-279">Pokud víte, že jste si jistí, že vaše aplikace se pořád spouští jenom na Blazor WebAssembly , můžete se rozhodnout provést synchronní volání interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="800f5-279">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="800f5-280">To má poněkud méně režie než provádění asynchronních volání a může mít za následek menší počet cyklů vykreslování, protože při čekání na výsledky neexistují žádné přechodné stavy.</span><span class="sxs-lookup"><span data-stu-id="800f5-280">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="800f5-281">Chcete-li provést synchronní volání z rozhraní .NET do JavaScriptu, přetypování <xref:Microsoft.JSInterop.IJSRuntime> na <xref:Microsoft.JSInterop.IJSInProcessRuntime> :</span><span class="sxs-lookup"><span data-stu-id="800f5-281">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="800f5-282">Při práci s nástrojem `IJSObjectReference` můžete provést synchronní volání přetypováním na `IJSInProcessObjectReference` .</span><span class="sxs-lookup"><span data-stu-id="800f5-282">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="800f5-283">Chcete-li uskutečnit synchronní volání z JavaScriptu do .NET, použijte `DotNet.invokeMethod` místo `DotNet.invokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="800f5-283">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="800f5-284">Synchronní volání fungují v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="800f5-284">Synchronous calls work if:</span></span>

* <span data-ttu-id="800f5-285">Aplikace běží na Blazor WebAssembly , ne Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="800f5-285">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="800f5-286">Volaná funkce vrací hodnotu synchronně (nejedná se o `async` metodu a nevrací rozhraní .NET <xref:System.Threading.Tasks.Task> nebo JavaScript `Promise` ).</span><span class="sxs-lookup"><span data-stu-id="800f5-286">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="800f5-287">Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="800f5-287">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="800f5-288">Zvažte provedení zařazování volání</span><span class="sxs-lookup"><span data-stu-id="800f5-288">Consider making unmarshalled calls</span></span>

<span data-ttu-id="800f5-289">Při spuštění v systému je možné provést nezařazená Blazor WebAssembly volání z rozhraní .NET do JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="800f5-289">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="800f5-290">Jedná se o synchronní volání, která neprovádějí serializaci argumentů nebo vrácených hodnot pomocí JSON.</span><span class="sxs-lookup"><span data-stu-id="800f5-290">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="800f5-291">Všechny aspekty správy paměti a překlady mezi reprezentacemi .NET a JavaScriptu jsou v rámci vývojářů.</span><span class="sxs-lookup"><span data-stu-id="800f5-291">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="800f5-292">I když použití `IJSUnmarshalledRuntime` má minimální režii spojené s těmito rozhraními API, jsou aktuálně nedokumentovaná rozhraní API pro interakci s těmito rozhraními API a můžou v budoucích verzích podléhat zásadním změnám.</span><span class="sxs-lookup"><span data-stu-id="800f5-292">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="800f5-293">Minimalizovat velikost stahovaných aplikací</span><span class="sxs-lookup"><span data-stu-id="800f5-293">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="800f5-294">Oříznutí mezilehlého jazyka (IL)</span><span class="sxs-lookup"><span data-stu-id="800f5-294">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="800f5-295">[Oříznutí nepoužívaných sestavení z Blazor WebAssembly aplikace](xref:blazor/host-and-deploy/configure-trimmer) zmenší velikost aplikace odebráním nepoužívaného kódu v binárních souborech aplikace.</span><span class="sxs-lookup"><span data-stu-id="800f5-295">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="800f5-296">Ve výchozím nastavení je oříznutí provedeno při publikování aplikace.</span><span class="sxs-lookup"><span data-stu-id="800f5-296">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="800f5-297">Pokud chcete využít oříznutí, publikujte aplikaci pro nasazení pomocí [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s možností [-c |--konfigurace](/dotnet/core/tools/dotnet-publish#options) nastavenou na `Release` :</span><span class="sxs-lookup"><span data-stu-id="800f5-297">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="800f5-298">Propojování IL (Intermediate Language)</span><span class="sxs-lookup"><span data-stu-id="800f5-298">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="800f5-299">[Propojení Blazor WebAssembly aplikace](xref:blazor/host-and-deploy/configure-linker) zmenšuje velikost Aplikace oříznutím nepoužívaného kódu v binárních souborech aplikace.</span><span class="sxs-lookup"><span data-stu-id="800f5-299">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="800f5-300">Ve výchozím nastavení je linker zprostředkujícího jazyka (IL) povolen pouze při sestavování v `Release` konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="800f5-300">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="800f5-301">Pokud to chcete využít, publikujte aplikaci pro nasazení pomocí [`dotnet publish`](/dotnet/core/tools/dotnet-publish) příkazu s možností [-c |--konfigurace](/dotnet/core/tools/dotnet-publish#options) nastavenou na `Release` :</span><span class="sxs-lookup"><span data-stu-id="800f5-301">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="800f5-302">Použít System.Text.Jsna</span><span class="sxs-lookup"><span data-stu-id="800f5-302">Use System.Text.Json</span></span>

<span data-ttu-id="800f5-303">Blazorimplementace interoperability JS spoléhá na <xref:System.Text.Json> , což je vysoce výkonné knihovny serializace JSON s neomezeným přidělením paměti.</span><span class="sxs-lookup"><span data-stu-id="800f5-303">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="800f5-304">Použití <xref:System.Text.Json> nemá za následek další velikost datové části aplikace nad přidáním jedné nebo více alternativních knihoven JSON.</span><span class="sxs-lookup"><span data-stu-id="800f5-304">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="800f5-305">Pokyny k migraci najdete v tématu [Postup migrace z `Newtonsoft.Json` na `System.Text.Json` ](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="800f5-305">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="800f5-306">Sestavení opožděného načtení</span><span class="sxs-lookup"><span data-stu-id="800f5-306">Lazy load assemblies</span></span>

<span data-ttu-id="800f5-307">Načíst sestavení za běhu, když jsou sestavení požadována trasou.</span><span class="sxs-lookup"><span data-stu-id="800f5-307">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="800f5-308">Další informace naleznete v tématu <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="800f5-308">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="800f5-309">Komprese</span><span class="sxs-lookup"><span data-stu-id="800f5-309">Compression</span></span>

<span data-ttu-id="800f5-310">Při Blazor WebAssembly publikování aplikace je výstup během publikování staticky komprimován, aby se snížila velikost aplikace a odstranila se režie pro kompresi za běhu.</span><span class="sxs-lookup"><span data-stu-id="800f5-310">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="800f5-311">Blazor spoléhá na server, aby provedl negotation obsahu a sloužil staticky komprimovaným souborům.</span><span class="sxs-lookup"><span data-stu-id="800f5-311">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="800f5-312">Po nasazení aplikace ověřte, jestli aplikace obsluhuje komprimované soubory.</span><span class="sxs-lookup"><span data-stu-id="800f5-312">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="800f5-313">Zkontrolujte kartu síť v Vývojářské nástroje prohlížeče a ověřte, zda jsou soubory obsluhovány `Content-Encoding: br` nebo `Content-Encoding: gz` .</span><span class="sxs-lookup"><span data-stu-id="800f5-313">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="800f5-314">Pokud hostitel neobsluhuje komprimované soubory, postupujte podle pokynů v tématu <xref:blazor/host-and-deploy/webassembly#compression> .</span><span class="sxs-lookup"><span data-stu-id="800f5-314">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="800f5-315">Zakázat nepoužívané funkce</span><span class="sxs-lookup"><span data-stu-id="800f5-315">Disable unused features</span></span>

<span data-ttu-id="800f5-316">Blazor WebAssemblymodul runtime obsahuje následující funkce .NET, které je možné zakázat, pokud je aplikace nepotřebuje pro menší velikost datové části:</span><span class="sxs-lookup"><span data-stu-id="800f5-316">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="800f5-317">K dispozici je datový soubor pro správné informace o časovém pásmu.</span><span class="sxs-lookup"><span data-stu-id="800f5-317">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="800f5-318">Pokud aplikace tuto funkci nevyžaduje, zvažte její zakázání nastavením `BlazorEnableTimeZoneSupport` vlastnosti MSBuild v souboru projektu aplikace na `false` :</span><span class="sxs-lookup"><span data-stu-id="800f5-318">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="800f5-319">Ve výchozím nastavení obsahuje Blazor WebAssembly prostředky globalizace vyžadované k zobrazení hodnot, jako jsou například data a měna, v jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="800f5-319">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="800f5-320">Pokud aplikace nevyžaduje lokalizaci, můžete [aplikaci nakonfigurovat tak, aby podporovala invariantní jazykovou verzi](xref:blazor/globalization-localization), která je založena na `en-US` jazykové verzi:</span><span class="sxs-lookup"><span data-stu-id="800f5-320">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="800f5-321">K zajištění správného fungování rozhraní API, jako je například práce, jsou zahrnuty informace o kolaci <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="800f5-321">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="800f5-322">Pokud jste si jisti, že aplikace nevyžaduje data kolace, zvažte její zakázání nastavením `BlazorWebAssemblyPreserveCollationData` vlastnosti MSBuild v souboru projektu aplikace na `false` :</span><span class="sxs-lookup"><span data-stu-id="800f5-322">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
