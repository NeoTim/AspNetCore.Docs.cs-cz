---
title: Vytvoření a použití Razor komponent ASP.NET Core
author: guardrex
description: Naučte se vytvářet a používat Razor komponenty, včetně toho, jak navazovat data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/index
ms.openlocfilehash: 1312ae531b0acb0ce09137cc2bae6299ddda5c32
ms.sourcegitcommit: e6b3dd344521ce320d5c2121bbc27c211df87e57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2020
ms.locfileid: "86468844"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="f5b1f-103">Vytvoření a použití Razor komponent ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f5b1f-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="f5b1f-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="f5b1f-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="f5b1f-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5b1f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="f5b1f-106">aplikace jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-106"> apps are built using *components*.</span></span> <span data-ttu-id="f5b1f-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="f5b1f-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="f5b1f-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="f5b1f-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="f5b1f-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="f5b1f-111">Component classes</span></span>

<span data-ttu-id="f5b1f-112">Komponenty jsou implementovány v [Razor](xref:mvc/views/razor) souborech komponenty ( `.razor` ) pomocí kombinace kódu jazyka C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="f5b1f-113">Komponenta v Blazor je formálně označována jako \* Razor Komponenta\*.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a>Razor<span data-ttu-id="f5b1f-114">syntaktick</span><span class="sxs-lookup"><span data-stu-id="f5b1f-114"> syntax</span></span>

Razor<span data-ttu-id="f5b1f-115">komponenty v Blazor aplikacích výrazně používají Razor syntaxi.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-115"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="f5b1f-116">Pokud nejste obeznámeni s Razor jazykem označení, doporučujeme <xref:mvc/views/razor> před pokračováním číst.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="f5b1f-117">Při přístupu k obsahu v Razor syntaxi věnujte zvláštní pozornost následujícím oddílům:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="f5b1f-118">[Direktivy](xref:mvc/views/razor#directives): `@` -předem stanovená vyhrazená klíčová slova, která obvykle mění způsob, jakým se kód komponenty analyzuje nebo funkce.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="f5b1f-119">[Atributy direktiv](xref:mvc/views/razor#directive-attributes): `@` -předem stanovená vyhrazená klíčová slova, která obvykle mění způsob, jakým jsou analyzovány prvky součásti nebo funkce.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="f5b1f-120">Názvy</span><span class="sxs-lookup"><span data-stu-id="f5b1f-120">Names</span></span>

<span data-ttu-id="f5b1f-121">Název součásti musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="f5b1f-122">Například `MyCoolComponent.razor` je platný a `myCoolComponent.razor` je neplatný.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="f5b1f-123">Směrování</span><span class="sxs-lookup"><span data-stu-id="f5b1f-123">Routing</span></span>

<span data-ttu-id="f5b1f-124">Směrování do Blazor se dosahuje tím, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="f5b1f-125">Když Razor je zkompilován soubor s [`@page`][9] direktivou, vygenerovaná třída je dána <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadáním šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="f5b1f-126">V době běhu Směrovač vyhledává třídy komponent pomocí <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="f5b1f-127">Další informace naleznete v tématu <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="f5b1f-128">Kód</span><span class="sxs-lookup"><span data-stu-id="f5b1f-128">Markup</span></span>

<span data-ttu-id="f5b1f-129">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="f5b1f-130">Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem *Razor* .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="f5b1f-131">Při kompilaci aplikace jsou značky HTML a vykreslování jazyka C# převedeny na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="f5b1f-132">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="f5b1f-133">Členy třídy komponenty jsou definovány v [`@code`][1] bloku.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="f5b1f-134">V [`@code`][1] bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="f5b1f-135">Je přípustný více než jeden [`@code`][1] blok.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="f5b1f-136">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí výrazů jazyka C#, které začínají na `@` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="f5b1f-137">Například pole C# se vykreslí pomocí předpony `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="f5b1f-138">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="f5b1f-139">`headingFontStyle`do hodnoty vlastnosti CSS pro `font-style` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="f5b1f-140">`headingText`k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="f5b1f-141">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="f5b1f-142">pak porovná nový strom vykreslování s předchozí a aplikuje všechny úpravy model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-142"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="f5b1f-143">Komponenty jsou běžné třídy jazyka C# a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="f5b1f-144">Komponenty, které tvoří webové stránky, se obvykle nacházejí ve `Pages` složce.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="f5b1f-145">Komponenty mimo stránku jsou často umístěny do `Shared` složky nebo vlastní složky přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="f5b1f-146">Jmenné prostory</span><span class="sxs-lookup"><span data-stu-id="f5b1f-146">Namespaces</span></span>

<span data-ttu-id="f5b1f-147">Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="f5b1f-148">Pokud je kořenový obor názvů aplikace `BlazorSample` a součást se nachází `Counter` ve `Pages` složce:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="f5b1f-149">`Counter`Obor názvů součásti je `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="f5b1f-150">Plně kvalifikovaný název typu komponenty je `BlazorSample.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="f5b1f-151">Pro vlastní složky, které obsahují součásti, přidejte do [`@using`][2] nadřazené komponenty nebo do souboru aplikace direktivu `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="f5b1f-152">V následujícím příkladu jsou komponenty ve `Components` složce k dispozici:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="f5b1f-153">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují [`@using`][2] direktivu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="f5b1f-154">Obor názvů komponenty, která se vytvořila, Razor je založený na (v pořadí podle priority):</span><span class="sxs-lookup"><span data-stu-id="f5b1f-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="f5b1f-155">[`@namespace`][8]označení v Razor souboru ( `.razor` ) značky ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="f5b1f-156">Projekt `RootNamespace` v souboru projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="f5b1f-157">Název projektu, pořízený z názvu souboru projektu ( `.csproj` ) a cesta z kořenového adresáře projektu k součásti.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="f5b1f-158">Například rozhraní překládá `{PROJECT ROOT}/Pages/Index.razor` ( `BlazorSample.csproj` ) na obor názvů `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="f5b1f-159">Komponenty následují pravidla vazeb názvů C#.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="f5b1f-160">Pro `Index` komponentu v tomto příkladu komponenty v oboru jsou všechny komponenty:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="f5b1f-161">Ve stejné složce, `Pages` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="f5b1f-162">Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="f5b1f-163">`global::`Kvalifikace není podporovaná.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="f5b1f-164">Import komponent s příkazy s aliasem [`using`](/dotnet/csharp/language-reference/keywords/using-statement) (například `@using Foo = Bar` ) není podporován.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="f5b1f-165">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="f5b1f-166">Například přidání `@using BlazorSample` a odkazování na `NavMenu` komponentu ( `NavMenu.razor` ) s `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="f5b1f-167">Podpora částečné třídy</span><span class="sxs-lookup"><span data-stu-id="f5b1f-167">Partial class support</span></span>

Razor<span data-ttu-id="f5b1f-168">komponenty jsou generovány jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-168"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="f5b1f-169">komponenty jsou vytvořeny jedním z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-169"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="f5b1f-170">Kód jazyka C# je definován v [`@code`][1] bloku s označením HTML a Razor kódem v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="f5b1f-171">šablony definují své Razor komponenty pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-171"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="f5b1f-172">Kód jazyka C# je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="f5b1f-173">Následující příklad ukazuje výchozí `Counter` komponentu s [`@code`][1] blokem v aplikaci vygenerovanou ze Blazor šablony.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="f5b1f-174">Kód HTML kódu Razor a kód jazyka C# jsou ve stejném souboru:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="f5b1f-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-175">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="f5b1f-176">`Counter`Komponentu lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="f5b1f-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="f5b1f-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-178">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="f5b1f-179">Podle potřeby přidejte všechny požadované obory názvů do souboru dílčí třídy.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="f5b1f-180">Mezi obvyklé obory názvů používané Razor komponentami patří:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="f5b1f-181">[`@using`][2]direktivy v `_Imports.razor` souboru jsou aplikovány pouze na Razor soubory ( `.razor` ), nikoli soubory jazyka C# ( `.cs` ).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="f5b1f-182">Zadat základní třídu</span><span class="sxs-lookup"><span data-stu-id="f5b1f-182">Specify a base class</span></span>

<span data-ttu-id="f5b1f-183">[`@inherits`][6]Direktiva se dá použít k určení základní třídy pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="f5b1f-184">Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase` k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="f5b1f-185">Základní třída by měla být odvozena od třídy <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="f5b1f-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="f5b1f-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-187">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="f5b1f-188">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="f5b1f-188">Use components</span></span>

<span data-ttu-id="f5b1f-189">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="f5b1f-190">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="f5b1f-191">Následující značka v `Pages/Index.razor` vykresluje `HeadingComponent` instanci:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="f5b1f-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="f5b1f-193">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="f5b1f-194">Přidání [`@using`][2] direktivy pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="f5b1f-195">Parametry</span><span class="sxs-lookup"><span data-stu-id="f5b1f-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="f5b1f-196">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="f5b1f-196">Route parameters</span></span>

<span data-ttu-id="f5b1f-197">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v [`@page`][9] direktivě.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="f5b1f-198">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="f5b1f-199">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-199">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="f5b1f-200">Volitelné parametry nejsou podporovány, takže [`@page`][9] v předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-200">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="f5b1f-201">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-201">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f5b1f-202">Druhá [`@page`][9] direktiva přijme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-202">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="f5b1f-203">*Catch-All* Parameter Syntax ( `*` / `**` ), která zachycuje cestu mezi více hranicemi složek, není **not** v Razor součástech ( `.razor` ) podporováno.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-203">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (`.razor`).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="f5b1f-204">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="f5b1f-204">Component parameters</span></span>

<span data-ttu-id="f5b1f-205">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-205">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="f5b1f-206">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-206">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="f5b1f-207">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-207">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="f5b1f-208">V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu `Title` vlastnosti `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-208">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="f5b1f-209">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-209">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="f5b1f-210">Nevytvářejte komponenty, které zapisují do vlastních *parametrů komponenty*, místo toho použijte soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-210">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="f5b1f-211">Další informace naleznete v části [Nevytvářet komponenty, které jsou zapsány do jejich vlastních vlastností parametrů](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-211">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="f5b1f-212">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="f5b1f-212">Child content</span></span>

<span data-ttu-id="f5b1f-213">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-213">Components can set the content of another component.</span></span> <span data-ttu-id="f5b1f-214">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-214">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="f5b1f-215">V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje, který představuje <xref:Microsoft.AspNetCore.Components.RenderFragment> segment uživatelského rozhraní pro vykreslení.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-215">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="f5b1f-216">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-216">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="f5b1f-217">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v panelu Bootstrap `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-217">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="f5b1f-218">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-218">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="f5b1f-219">Vlastnost, která přijímá <xref:Microsoft.AspNetCore.Components.RenderFragment> obsah, musí být pojmenována `ChildContent` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-219">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="f5b1f-220">`ParentComponent`V ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř `<ChildComponent>` značek.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-220">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="f5b1f-221">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-221">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="f5b1f-222">Vzhledem k způsobu, jakým Blazor vykresluje podřízený obsah, vykreslování komponent uvnitř `for` smyčky vyžaduje proměnnou místního indexu, pokud se v obsahu podřízené součásti používá proměnná přírůstkového cyklu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-222">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="f5b1f-223">Alternativně můžete použít `foreach` smyčku s <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-223">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="f5b1f-224">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="f5b1f-224">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="f5b1f-225">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-225">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="f5b1f-226">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí [`@attributes`][3] Razor direktivy.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-226">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="f5b1f-227">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-227">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="f5b1f-228">Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-228">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="f5b1f-229">V následujícím příkladu první `<input>` prvek ( `id="useIndividualParams"` ) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element ( `id="useAttributesDict"` ) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-229">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="f5b1f-230">Typ parametru musí být implementován `IEnumerable<KeyValuePair<string, object>>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-230">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="f5b1f-231">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-231">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="f5b1f-232">Vykreslené `<input>` elementy pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-232">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="f5b1f-233">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributu s <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> vlastností nastavenou na `true` :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-233">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="f5b1f-234"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Vlastnost on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-234">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="f5b1f-235">Komponenta může definovat pouze jeden parametr s <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-235">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="f5b1f-236">Typ vlastnosti používaný pomocí <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-236">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="f5b1f-237">`IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-237">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="f5b1f-238">Pozice [`@attributes`][3] relativní vzhledem k poloze atributů elementu je důležitá.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-238">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="f5b1f-239">Když [`@attributes`][3] jsou splatted na elementu, atributy jsou zpracovávány zprava doleva (poslední až první).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-239">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="f5b1f-240">Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-240">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="f5b1f-241">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-241">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="f5b1f-242">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-242">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="f5b1f-243">`Child` `extra` Atribut komponenty je nastaven na hodnotu napravo od [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-243">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="f5b1f-244">`Parent`Vykreslená komponenta `<div>` obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):</span><span class="sxs-lookup"><span data-stu-id="f5b1f-244">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="f5b1f-245">V následujícím příkladu `extra` je pořadí a [`@attributes`][3] obrácené v `Child` komponentě `<div>` :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-245">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="f5b1f-246">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-246">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="f5b1f-247">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-247">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="f5b1f-248">Vykreslená `<div>` `Parent` Komponenta obsahuje, `extra="10"` když je předána pomocí dalšího atributu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-248">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="f5b1f-249">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="f5b1f-249">Capture references to components</span></span>

<span data-ttu-id="f5b1f-250">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-250">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="f5b1f-251">Zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-251">To capture a component reference:</span></span>

* <span data-ttu-id="f5b1f-252">Přidejte [`@ref`][4] atribut do podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-252">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="f5b1f-253">Definujte pole stejného typu jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-253">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="f5b1f-254">Při vykreslení komponenty `loginDialog` je pole vyplněno `MyLoginDialog` instancí podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-254">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="f5b1f-255">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-255">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f5b1f-256">`loginDialog`Proměnná je naplněna pouze po vykreslení komponenty a její výstup obsahuje `MyLoginDialog` element.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-256">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="f5b1f-257">Dokud se komponenta nevykresluje, neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-257">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="f5b1f-258">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [ `OnAfterRenderAsync` `OnAfterRender` metody nebo](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-258">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="f5b1f-259">Chcete-li použít referenční proměnnou s obslužnou rutinou události, použijte výraz lambda nebo přiřaďte delegáta obslužné rutiny události v [ `OnAfterRenderAsync` `OnAfterRender` metodách nebo](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-259">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="f5b1f-260">Tím se zajistí, že referenční proměnná je přiřazena před přiřazením obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-260">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="f5b1f-261">Chcete-li odkazovat na součásti ve smyčce, viz [zachytit odkazy na více podobných podřízených komponent (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-261">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="f5b1f-262">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-262">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="f5b1f-263">Odkazy na součásti nejsou předány kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-263">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="f5b1f-264">Odkazy na komponenty se používají pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-264">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="f5b1f-265">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-265">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="f5b1f-266">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-266">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="f5b1f-267">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-267">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="f5b1f-268">Kontext synchronizace</span><span class="sxs-lookup"><span data-stu-id="f5b1f-268">Synchronization context</span></span>

Blazor<span data-ttu-id="f5b1f-269">používá kontext synchronizace ( <xref:System.Threading.SynchronizationContext> ) k vykonání jediného logického vlákna provádění.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-269"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="f5b1f-270">[Metody životního cyklu](xref:blazor/components/lifecycle) komponenty a všechna zpětná volání událostí, která jsou vyvolána, Blazor jsou spouštěna v kontextu synchronizace.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-270">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor Server<span data-ttu-id="f5b1f-271">kontext synchronizace se pokouší emulovat prostředí s jedním vláknem tak, aby přesně odpovídal modelu webového sestavení v prohlížeči, který je jediným vláknem.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-271">'s synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="f5b1f-272">V jakémkoli daném časovém okamžiku se práce provádí v přesně jednom vlákně, což dává dojem o jednom logickém vlákně.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-272">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="f5b1f-273">Žádné dvě operace se neprovádějí současně.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-273">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="f5b1f-274">Vyhněte se voláním blokujícím vlákna</span><span class="sxs-lookup"><span data-stu-id="f5b1f-274">Avoid thread-blocking calls</span></span>

<span data-ttu-id="f5b1f-275">Obecně platí, že Nevolejte následující metody.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-275">Generally, don't call the following methods.</span></span> <span data-ttu-id="f5b1f-276">Následující metody zablokují vlákno, takže aplikace brání v pokračování práce, dokud se podklady <xref:System.Threading.Tasks.Task> nedokončí:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-276">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="f5b1f-277">Vyvolat metody komponenty externě na stav aktualizace</span><span class="sxs-lookup"><span data-stu-id="f5b1f-277">Invoke component methods externally to update state</span></span>

<span data-ttu-id="f5b1f-278">V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte `InvokeAsync` metodu, která odesílá Blazor kontext synchronizace.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-278">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="f5b1f-279">Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-279">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="f5b1f-280">Zaregistrujte `NotifierService` :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-280">Register the `NotifierService`:</span></span>

* <span data-ttu-id="f5b1f-281">V nástroji Blazor WebAssembly Zaregistrujte službu jako singleton v `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-281">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="f5b1f-282">V nástroji Blazor Server Zaregistrujte službu jako vymezenou v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-282">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="f5b1f-283">`NotifierService`K aktualizaci součásti použijte.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-283">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="f5b1f-284">V předchozím příkladu `NotifierService` vyvolá `OnNotify` metodu komponenty mimo Blazor kontext synchronizace.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-284">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="f5b1f-285">`InvokeAsync`slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-285">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="f5b1f-286">Použití \@ klíče k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="f5b1f-286">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="f5b1f-287">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent se Blazor musí rozhodnout, který z předchozích prvků nebo komponent může být zachován a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-287">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="f5b1f-288">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-288">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="f5b1f-289">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-289">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="f5b1f-290">Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-290">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="f5b1f-291">Když se komponenta znovu vykreslí, může se tato `<DetailsEditor>` součást změnit, aby přijímala jiné `Details` hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-291">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="f5b1f-292">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-292">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="f5b1f-293">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-293">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="f5b1f-294">Proces mapování lze řídit pomocí [`@key`][5] atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-294">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="f5b1f-295">[`@key`][5]způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-295">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="f5b1f-296">Když se `People` kolekce změní, rozdílový algoritmus zachovává přidružení mezi `<DetailsEditor>` instancemi a `person` instancemi:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-296">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="f5b1f-297">Pokud `Person` se ze `People` seznamu odstraní, `<DetailsEditor>` z uživatelského rozhraní se odebere jenom odpovídající instance.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-297">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="f5b1f-298">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-298">Other instances are left unchanged.</span></span>
* <span data-ttu-id="f5b1f-299">Pokud `Person` je vložena na nějaké místo v seznamu, `<DetailsEditor>` je do příslušné pozice vložena jedna nová instance.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-299">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="f5b1f-300">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-300">Other instances are left unchanged.</span></span>
* <span data-ttu-id="f5b1f-301">Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-301">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="f5b1f-302">V některých scénářích použití [`@key`][5] minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-302">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f5b1f-303">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-303">Keys are local to each container element or component.</span></span> <span data-ttu-id="f5b1f-304">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-304">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="f5b1f-305">Kdy použít \@ klíč</span><span class="sxs-lookup"><span data-stu-id="f5b1f-305">When to use \@key</span></span>

<span data-ttu-id="f5b1f-306">Obvykle má smysl použít [`@key`][5] při každém vykreslení seznamu (například v bloku [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) a existuje vhodná hodnota pro definování [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-306">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="f5b1f-307">Můžete také použít [`@key`][5] k zabránění Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-307">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="f5b1f-308">Pokud se `@currentPerson` změní, [`@key`][5] direktiva Attribute vynutí Blazor zrušení celého `<div>` a jeho následníků a znovu sestaví podstrom v uživatelském rozhraní novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-308">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="f5b1f-309">To může být užitečné, pokud potřebujete zaručit, že se při změnách nezachovají stav uživatelského rozhraní `@currentPerson` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-309">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="f5b1f-310">Kdy nepoužívat \@ klíč</span><span class="sxs-lookup"><span data-stu-id="f5b1f-310">When not to use \@key</span></span>

<span data-ttu-id="f5b1f-311">Při rozdílech s se účtují náklady na výkon [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-311">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="f5b1f-312">Náklady na výkon nejsou velké, ale zadávejte jenom [`@key`][5] v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-312">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="f5b1f-313">I v případě [`@key`][5] , že není použit, Blazor zachová podřízený element a instance komponenty co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-313">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="f5b1f-314">Jedinou výhodou použití [`@key`][5] je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-314">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="f5b1f-315">Jaké hodnoty se mají použít pro \@ klíč</span><span class="sxs-lookup"><span data-stu-id="f5b1f-315">What values to use for \@key</span></span>

<span data-ttu-id="f5b1f-316">Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-316">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="f5b1f-317">Instance objektů modelu (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-317">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="f5b1f-318">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-318">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="f5b1f-319">Jedinečné identifikátory (například hodnoty primárního klíče typu `int` , `string` nebo `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-319">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="f5b1f-320">Zajistěte, aby hodnoty použité pro [`@key`][5] nekolidovat.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-320">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="f5b1f-321">Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty konfliktu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo součásti na nové prvky nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-321">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="f5b1f-322">Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-322">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="f5b1f-323">Nevytvářejte komponenty, které zapisují do vlastností vlastního parametru.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-323">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="f5b1f-324">Parametry jsou přepsány za následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-324">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="f5b1f-325">Obsah podřízené komponenty je vykreslen pomocí <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-325">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="f5b1f-326"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána v nadřazené komponentě.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-326"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="f5b1f-327">Parametry jsou resetovány, protože nadřazená komponenta je znovu vygenerována <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> , když je volána, a podřízené součásti jsou zadány nové hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-327">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="f5b1f-328">Vezměte v úvahu následující `Expander` komponentu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-328">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="f5b1f-329">Vykreslí podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-329">Renders child content.</span></span>
* <span data-ttu-id="f5b1f-330">Přepíná zobrazení podřízeného obsahu s parametrem součásti.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-330">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (<code>Expanded</code> = @Expanded)</h2>
        </div>

        @if (Expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="f5b1f-331">`Expander`Komponenta je přidána do nadřazené komponenty, která může zavolat <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="f5b1f-331">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="f5b1f-332">Zpočátku se `Expander` komponenty chovají nezávisle při `Expanded` přepínání jejich vlastností.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-332">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="f5b1f-333">Podřízené komponenty udržují své stavy podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-333">The child components maintain their states as expected.</span></span> <span data-ttu-id="f5b1f-334">Když <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> je volána v nadřazeném prvku, `Expanded` parametr první podřízené komponenty se obnoví zpět na původní hodnotu ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-334">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="f5b1f-335">Hodnota druhé `Expander` součásti `Expanded` není resetována, protože ve druhé komponentě není vykreslen žádný podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-335">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="f5b1f-336">Chcete-li zachovat stav v předchozím scénáři, použijte *soukromé pole* v `Expander` součásti k údržbě jeho přepnutého stavu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-336">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="f5b1f-337">Následující revidovaná `Expander` součást:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-337">The following revised `Expander` component:</span></span>

* <span data-ttu-id="f5b1f-338">Akceptuje `Expanded` hodnotu parametru komponenty z nadřazené položky.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-338">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="f5b1f-339">Přiřadí hodnotu parametru komponenty k *privátnímu poli* ( `expanded` ) v [události s inicializací](xref:blazor/components/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-339">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="f5b1f-340">Pomocí soukromého pole udržuje stav interního přepínání.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-340">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle" class="card text-white bg-success mb-3">
    <div class="card-body">
        <div class="panel-heading">
            <h2>Toggle (<code>expanded</code> = @expanded)</h2>
        </div>

        @if (expanded)
        {
            <div class="card-text">
                @ChildContent
            </div>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="apply-an-attribute"></a><span data-ttu-id="f5b1f-341">Použití atributu</span><span class="sxs-lookup"><span data-stu-id="f5b1f-341">Apply an attribute</span></span>

<span data-ttu-id="f5b1f-342">Atributy lze použít pro Razor součásti s [`@attribute`][7] direktivou.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-342">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="f5b1f-343">Následující příklad používá [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atribut pro třídu komponenty:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-343">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="f5b1f-344">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="f5b1f-344">Conditional HTML element attributes</span></span>

<span data-ttu-id="f5b1f-345">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-345">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="f5b1f-346">Pokud je hodnota `false` nebo `null` , atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-346">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="f5b1f-347">Pokud je hodnota `true` , je vygenerována hodnota atributu minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-347">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="f5b1f-348">V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-348">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="f5b1f-349">Pokud `IsCompleted` je `true` , zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-349">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="f5b1f-350">Pokud `IsCompleted` je `false` , zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-350">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="f5b1f-351">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-351">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="f5b1f-352">Některé atributy HTML, například [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) , nefungují správně, pokud je typ .NET `bool` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-352">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="f5b1f-353">V těchto případech použijte `string` typ místo typu `bool` .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-353">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="f5b1f-354">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="f5b1f-354">Raw HTML</span></span>

<span data-ttu-id="f5b1f-355">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-355">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="f5b1f-356">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-356">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="f5b1f-357">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-357">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="f5b1f-358">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="f5b1f-358">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="f5b1f-359">Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-359">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a>Razor<span data-ttu-id="f5b1f-360">šablony</span><span class="sxs-lookup"><span data-stu-id="f5b1f-360"> templates</span></span>

<span data-ttu-id="f5b1f-361">Fragmenty vykreslování lze definovat pomocí Razor syntaxe šablony.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-361">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="f5b1f-362">Šablony představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-362"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="f5b1f-363">Následující příklad ukazuje, jak zadat <xref:Microsoft.AspNetCore.Components.RenderFragment> a <xref:Microsoft.AspNetCore.Components.RenderFragment%601> hodnoty a vykreslit šablony přímo v součásti.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-363">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="f5b1f-364">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/components/templated-components).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-364">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="f5b1f-365">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-365">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="f5b1f-366">Statické prostředky</span><span class="sxs-lookup"><span data-stu-id="f5b1f-366">Static assets</span></span>

Blazor<span data-ttu-id="f5b1f-367">postupuje podle konvence ASP.NET Core aplikací, které umísťují statické prostředky do [ `web root (wwwroot)` složky](xref:fundamentals/index#web-root)projektu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-367"> follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="f5b1f-368">Použijte základní cestu ( `/` ) pro odkaz na webový kořenový adresář pro statický prostředek.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-368">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="f5b1f-369">V následujícím příkladu `logo.png` je fyzicky umístěný ve `{PROJECT ROOT}/wwwroot/images` složce:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-369">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="f5b1f-370">komponenty **nepodporují** vlnové lomítko ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-370"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="f5b1f-371">Informace o nastavení základní cesty aplikace najdete v tématu <xref:blazor/host-and-deploy/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="f5b1f-371">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="f5b1f-372">V součástech nejsou podporovány pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-372">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="f5b1f-373">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro)nepodporuje se v Razor součástech ( `.razor` soubory).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-373">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="f5b1f-374">Chcete-li poskytnout funkci, jako je například Pomocník pro Blazor vytváření značek, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-374">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="f5b1f-375">Obrázky ve formátu SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="f5b1f-375">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="f5b1f-376">Vzhledem k tomu, že Blazor VYKRESLUJE HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) ( `.svg` ), jsou podporovány prostřednictvím `<img>` značky:</span><span class="sxs-lookup"><span data-stu-id="f5b1f-376">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="f5b1f-377">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů ( `.css` ):</span><span class="sxs-lookup"><span data-stu-id="f5b1f-377">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="f5b1f-378">Vložené značky SVG se však ve všech scénářích nepodporují.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-378">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="f5b1f-379">Pokud `<svg>` značku umístíte přímo do souboru komponenty ( `.razor` ), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů ještě není podporováno.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-379">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="f5b1f-380">Například `<use>` značky nejsou aktuálně dodržovány a [`@bind`][10] nelze je použít s některými značkami SVG.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-380">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="f5b1f-381">Další informace naleznete v tématu [Podpora SVG in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="f5b1f-381">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5b1f-382">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f5b1f-382">Additional resources</span></span>

* <span data-ttu-id="f5b1f-383"><xref:blazor/security/server/threat-mitigation>: Obsahuje pokyny pro vytváření Blazor Server aplikací, které se musí soupeří s vyčerpáním prostředků.</span><span class="sxs-lookup"><span data-stu-id="f5b1f-383"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
