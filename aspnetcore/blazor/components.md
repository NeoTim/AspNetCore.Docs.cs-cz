---
title: Vytváření a používání komponent ASP.NET Core Razor
author: guardrex
description: Zjistěte, jak vytvořit a používat komponenty Razor, včetně jak vázat na data, zpracování událostí a spravovat životní cykly komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/components
ms.openlocfilehash: c52f23ea319d30d871ecdfc9648a4e30aa877324
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538509"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="17c29-103">Vytváření a používání komponent ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="17c29-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="17c29-104">Podle [Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="17c29-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="17c29-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17c29-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="17c29-106">Blazor aplikace se vytvářejí pomocí *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="17c29-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="17c29-107">Komponenta je samostatná blok uživatelského rozhraní (UI), například stránky, dialogové okno nebo formuláře.</span><span class="sxs-lookup"><span data-stu-id="17c29-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="17c29-108">Komponenta obsahuje kód HTML a zpracování logiku potřebnou k vložení dat nebo v reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="17c29-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="17c29-109">Součásti jsou flexibilní a jednoduchý.</span><span class="sxs-lookup"><span data-stu-id="17c29-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="17c29-110">Mohou být vnořené, znovu použít a sdílet mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="17c29-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="17c29-111">Třídy součásti</span><span class="sxs-lookup"><span data-stu-id="17c29-111">Component classes</span></span>

<span data-ttu-id="17c29-112">Součásti jsou implementovány v [Razor](xref:mvc/views/razor) soubory součástí ( *.razor*) pomocí kombinace C# a značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="17c29-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="17c29-113">Součástí Blazor formálně označovány jako *Razor komponenty*.</span><span class="sxs-lookup"><span data-stu-id="17c29-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="17c29-114">Dají se vytvářet komponenty pomocí *.cshtml* příponu souboru, tak dlouho, dokud soubory jsou označeny jako soubory součástí Razor pomocí `_RazorComponentInclude` vlastnost MSBuild.</span><span class="sxs-lookup"><span data-stu-id="17c29-114">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="17c29-115">Například aplikaci, která určuje, že všechny *.cshtml* soubory pod *stránky* složky mají být považována za soubory součástí Razor:</span><span class="sxs-lookup"><span data-stu-id="17c29-115">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="17c29-116">Uživatelské rozhraní pro součást je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="17c29-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="17c29-117">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="17c29-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="17c29-118">Při kompilaci aplikace, bude značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="17c29-119">Název generované třídy odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="17c29-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="17c29-120">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="17c29-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="17c29-121">V `@code` bloku, stav komponent (vlastnosti, pole) zadán s parametrem metody pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="17c29-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="17c29-122">Více než jeden `@code` blok je povolený.</span><span class="sxs-lookup"><span data-stu-id="17c29-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="17c29-123">V předchozích verzích technologie ASP.NET Core `@functions` bloky byly použity ke stejnému účelu jako `@code` bloky.</span><span class="sxs-lookup"><span data-stu-id="17c29-123">In previous versions of ASP.NET Core, `@functions` blocks were used for the same purpose as `@code` blocks.</span></span> <span data-ttu-id="17c29-124">`@functions` bloky i nadále fungovat, ale doporučujeme používat `@code` směrnice.</span><span class="sxs-lookup"><span data-stu-id="17c29-124">`@functions` blocks continue to work, but we recommend using the `@code` directive.</span></span>

<span data-ttu-id="17c29-125">Komponenta členy můžete poté použita jako tato součást je vykreslování pomocí logiky C# výrazy, které začínají `@`.</span><span class="sxs-lookup"><span data-stu-id="17c29-125">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="17c29-126">Například C# pole se vykreslí vložením prefixu `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="17c29-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="17c29-127">Následující příklad vyhodnotí a vykreslí:</span><span class="sxs-lookup"><span data-stu-id="17c29-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="17c29-128">`_headingFontStyle` Hodnota vlastnosti šablon stylů CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="17c29-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="17c29-129">`_headingText` k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="17c29-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="17c29-130">Po se zpočátku zobrazí komponentu obnoví komponenty jeho vykreslení stromu v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="17c29-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="17c29-131">Blazor poté porovnává větve vykreslení oproti předchozímu a použije všechny změny do prohlížeče Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="17c29-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="17c29-132">Součásti jsou běžné C# třídy a může být umístěna kdekoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="17c29-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="17c29-133">Součásti, které se obvykle vytvářejí webové stránky se nacházejí v *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="17c29-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="17c29-134">Non stránka součásti jsou často umístěny do *Shared* nebo vlastní složky přidán do projektu.</span><span class="sxs-lookup"><span data-stu-id="17c29-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="17c29-135">Pokud chcete použít vlastní složku, buď přidejte vlastní složky oboru názvů na nadřazenou komponentu nebo na aplikaci *_Imports.razor* souboru.</span><span class="sxs-lookup"><span data-stu-id="17c29-135">To use a custom folder, either add the custom folder's namespace to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="17c29-136">Například následující obor názvů umožňuje součástí *součásti* složky, které jsou k dispozici, když je aplikace kořenový obor názvů `WebApplication`:</span><span class="sxs-lookup"><span data-stu-id="17c29-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="17c29-137">Integrovat komponenty do aplikace Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="17c29-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="17c29-138">Komponenty pomocí stávající aplikace Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="17c29-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="17c29-139">Není nutné pro přepsání existujících stránek nebo zobrazení Razor komponent.</span><span class="sxs-lookup"><span data-stu-id="17c29-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="17c29-140">Při zobrazení stránky nebo zobrazení se komponenty jsou předkreslených ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="17c29-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="17c29-141">K vykreslení komponenty z stránku nebo zobrazení, použijte `RenderComponentAsync<TComponent>` metodu helper HTML:</span><span class="sxs-lookup"><span data-stu-id="17c29-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="17c29-142">Při zobrazení stránky a můžou používat komponenty, neplatí první.</span><span class="sxs-lookup"><span data-stu-id="17c29-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="17c29-143">Součásti nelze použít zobrazení a stránky konkrétní scénáře, jako je částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="17c29-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="17c29-144">Chcete-li použít logiku z částečného zobrazení v komponentě, faktor si logiky částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="17c29-145">Další informace o způsobu vykreslené a komponenty stav součásti je spravováno v Blazor serverové aplikace, najdete v článku <xref:blazor/hosting-models> článku.</span><span class="sxs-lookup"><span data-stu-id="17c29-145">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="using-components"></a><span data-ttu-id="17c29-146">Pomocí komponent</span><span class="sxs-lookup"><span data-stu-id="17c29-146">Using components</span></span>

<span data-ttu-id="17c29-147">Součásti můžete zahrnout další součásti je deklarací pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="17c29-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="17c29-148">Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.</span><span class="sxs-lookup"><span data-stu-id="17c29-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="17c29-149">Následující kód v *Index.razor* vykreslí `HeadingComponent` instance:</span><span class="sxs-lookup"><span data-stu-id="17c29-149">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="17c29-150">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="17c29-150">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

## <a name="component-parameters"></a><span data-ttu-id="17c29-151">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="17c29-151">Component parameters</span></span>

<span data-ttu-id="17c29-152">Může mít komponenty *parametry komponenty*, které jsou definovány pomocí vlastnosti (obvykle *neveřejné*) na komponentní třída s `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="17c29-152">Components can have *component parameters*, which are defined using properties (usually *non-public*) on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="17c29-153">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="17c29-153">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="17c29-154">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="17c29-154">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="17c29-155">V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` vlastnost `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="17c29-155">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="17c29-156">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="17c29-156">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="17c29-157">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="17c29-157">Child content</span></span>

<span data-ttu-id="17c29-158">Součásti můžete nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="17c29-158">Components can set the content of another component.</span></span> <span data-ttu-id="17c29-159">Přiřazení součásti najdete zde obsah mezi značky, které určují přijímající komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-159">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="17c29-160">V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="17c29-160">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="17c29-161">Hodnota `ChildContent` je umístěn ve značkách komponenty ve kterém má být vykreslen obsah.</span><span class="sxs-lookup"><span data-stu-id="17c29-161">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="17c29-162">Hodnota `ChildContent` přijme od nadřazené komponenty a vykreslit v rámci panelu Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="17c29-162">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="17c29-163">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="17c29-163">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="17c29-164">Vlastnost příjmu `RenderFragment` obsahu musí mít název `ChildContent` konvencí.</span><span class="sxs-lookup"><span data-stu-id="17c29-164">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="17c29-165">Následující `ParentComponent` můžete zadat obsah pro vykreslování `ChildComponent` tak, že je obsah uvnitř `<ChildComponent>` značky.</span><span class="sxs-lookup"><span data-stu-id="17c29-165">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="17c29-166">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="17c29-166">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="data-binding"></a><span data-ttu-id="17c29-167">Datová vazba</span><span class="sxs-lookup"><span data-stu-id="17c29-167">Data binding</span></span>

<span data-ttu-id="17c29-168">Vazba dat na komponent a prvky modelu DOM se dosahuje pomocí `@bind` atribut.</span><span class="sxs-lookup"><span data-stu-id="17c29-168">Data binding to both components and DOM elements is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="17c29-169">Následující příklad vytvoří vazbu `_italicsCheck` pole na zaškrtávací políčko zaškrtnuto, stav:</span><span class="sxs-lookup"><span data-stu-id="17c29-169">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="17c29-170">Při zaškrtnutí políčka je a zrušte zaškrtnutí, hodnota vlastnosti je aktualizována na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="17c29-170">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="17c29-171">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní, pouze v případě, že součást je vykresleno, ne v reakci na měnící se hodnota vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="17c29-171">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="17c29-172">Protože komponenty vykreslování sami po spuštění kódu obslužné rutiny události, aktualizace vlastností se obvykle projeví v uživatelském rozhraní ihned.</span><span class="sxs-lookup"><span data-stu-id="17c29-172">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="17c29-173">Pomocí `@bind` s `CurrentValue` vlastnosti (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="17c29-173">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="17c29-174">Při vykreslování komponentu `value` elementu input pochází z `CurrentValue` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="17c29-174">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="17c29-175">Když uživatel zadá v textovém poli `onchange` událost se aktivuje a `CurrentValue` je nastavena na hodnotu změněné.</span><span class="sxs-lookup"><span data-stu-id="17c29-175">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="17c29-176">Ve skutečnosti je generování kódu poněkud složitější, protože `@bind` zpracovává několik případů, kdy jsou provedeny převody typu.</span><span class="sxs-lookup"><span data-stu-id="17c29-176">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="17c29-177">V zásadě `@bind` přidruží aktuální hodnotu výrazu s `value` obslužné rutiny a atributu změny pomocí zaregistrovaná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="17c29-177">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="17c29-178">Kromě zpracování `onchange` události s `@bind` syntaxi, vlastnosti nebo pole lze navázat tak, že určíte pomocí jiných událostí `@bind-value` atributem `event` parametru.</span><span class="sxs-lookup"><span data-stu-id="17c29-178">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an `@bind-value` attribute with an `event` parameter.</span></span> <span data-ttu-id="17c29-179">Následující příklad vytvoří vazbu `CurrentValue` vlastnost `oninput` události:</span><span class="sxs-lookup"><span data-stu-id="17c29-179">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="17c29-180">Na rozdíl od `onchange`, což je vyvoláno, pokud prvek ztratí fokus, `oninput` je aktivována, když hodnota textovém poli změny.</span><span class="sxs-lookup"><span data-stu-id="17c29-180">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="17c29-181">**Formátovací řetězce**</span><span class="sxs-lookup"><span data-stu-id="17c29-181">**Format strings**</span></span>

<span data-ttu-id="17c29-182">Vytváření datových vazeb funguje s <xref:System.DateTime> řetězce formátu.</span><span class="sxs-lookup"><span data-stu-id="17c29-182">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="17c29-183">V tuto chvíli nejsou k dispozici jiných výrazech formátu, například měny nebo číselných formátů.</span><span class="sxs-lookup"><span data-stu-id="17c29-183">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="17c29-184">`@bind:format` Atribut specifikuje formát data použít `value` z `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="17c29-184">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="17c29-185">Formát slouží také k analýze hodnotu při `onchange` dojde k události.</span><span class="sxs-lookup"><span data-stu-id="17c29-185">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="17c29-186">**Parametry komponenty**</span><span class="sxs-lookup"><span data-stu-id="17c29-186">**Component parameters**</span></span>

<span data-ttu-id="17c29-187">Vazba také rozpozná parametry komponenty, kde `@bind-{property}` mohl vytvořit vazbu vlastnosti komponentami.</span><span class="sxs-lookup"><span data-stu-id="17c29-187">Binding also recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="17c29-188">Následující podřízené součásti (`ChildComponent`) má `Year` parametr součásti a `YearChanged` zpětného volání:</span><span class="sxs-lookup"><span data-stu-id="17c29-188">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="17c29-189">`EventCallback<T>` je podrobně [EventCallback](#eventcallback) oddílu.</span><span class="sxs-lookup"><span data-stu-id="17c29-189">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="17c29-190">Následující nadřazené komponenty používá `ChildComponent` a vytvoří vazbu `ParentYear` parametr z nadřazeného `Year` parametru u podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="17c29-190">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="17c29-191">Načítají `ParentComponent` vytváří následující značky:</span><span class="sxs-lookup"><span data-stu-id="17c29-191">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="17c29-192">Pokud hodnota `ParentYear` vlastnost se změní tak, že vyberete tlačítko v `ParentComponent`, `Year` vlastnost `ChildComponent` se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="17c29-192">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="17c29-193">Nová hodnota `Year` se vykreslí v uživatelském rozhraní při `ParentComponent` je rerendered:</span><span class="sxs-lookup"><span data-stu-id="17c29-193">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="17c29-194">`Year` Parametr je s možností vazby, protože má doprovodná `YearChanged` událost, která odpovídá typu používaného `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="17c29-194">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="17c29-195">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` je v podstatě ekvivalentní zápisu:</span><span class="sxs-lookup"><span data-stu-id="17c29-195">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="17c29-196">Obecně platí, vlastnost může být vázána na odpovídající obslužná rutina události pomocí `@bind-property:event` atribut.</span><span class="sxs-lookup"><span data-stu-id="17c29-196">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="17c29-197">Například vlastnost `MyProp` může být vázaný na `MyEventHandler` pomocí následující dva atributy:</span><span class="sxs-lookup"><span data-stu-id="17c29-197">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="17c29-198">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="17c29-198">Event handling</span></span>

<span data-ttu-id="17c29-199">Součásti syntaxe Razor poskytují funkce zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="17c29-199">Razor components provide event handling features.</span></span> <span data-ttu-id="17c29-200">Atribut HTML elementu s názvem `on<event>` (například `onclick` a `onsubmit`) s hodnotou typu delegáta Razor součásti považuje za hodnotu atributu obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="17c29-200">For an HTML element attribute named `on<event>` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="17c29-201">Název atributu vždy začíná `@on`.</span><span class="sxs-lookup"><span data-stu-id="17c29-201">The attribute's name always starts with `@on`.</span></span>

<span data-ttu-id="17c29-202">Následující kód volá `UpdateHeading` metodu po výběru tlačítka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="17c29-202">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="@UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="17c29-203">Následující kód volá `CheckChanged` metoda při změně zaškrtávacího políčka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="17c29-203">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="@CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="17c29-204">Obslužné rutiny událostí může být také asynchronní a zpět <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="17c29-204">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="17c29-205">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="17c29-205">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="17c29-206">Výjimky se protokolují, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="17c29-206">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="17c29-207">V následujícím příkladu `UpdateHeading` se asynchronně volá při výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="17c29-207">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="@UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="17c29-208">Pro některé události nejsou povoleny typy argumentů události specifické pro události.</span><span class="sxs-lookup"><span data-stu-id="17c29-208">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="17c29-209">Pokud přístup k jednomu z těchto typů událostí není nezbytné, není potřeba ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="17c29-209">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="17c29-210">Podporované [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="17c29-210">Supported [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) are shown in the following table.</span></span>

| <span data-ttu-id="17c29-211">Událost</span><span class="sxs-lookup"><span data-stu-id="17c29-211">Event</span></span> | <span data-ttu-id="17c29-212">Třída</span><span class="sxs-lookup"><span data-stu-id="17c29-212">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="17c29-213">Schránka</span><span class="sxs-lookup"><span data-stu-id="17c29-213">Clipboard</span></span> | `UIClipboardEventArgs` |
| <span data-ttu-id="17c29-214">Přetažení</span><span class="sxs-lookup"><span data-stu-id="17c29-214">Drag</span></span>  | <span data-ttu-id="17c29-215">`UIDragEventArgs` &ndash; `DataTransfer` slouží k ukládání dat Přetahované během operace přetažení a může obsahovat jeden nebo více `UIDataTransferItem`.</span><span class="sxs-lookup"><span data-stu-id="17c29-215">`UIDragEventArgs` &ndash; `DataTransfer` is used to hold the dragged data during a drag and drop operation and may hold one or more `UIDataTransferItem`.</span></span> <span data-ttu-id="17c29-216">`UIDataTransferItem` představuje jeden přetáhněte datovou položku.</span><span class="sxs-lookup"><span data-stu-id="17c29-216">`UIDataTransferItem` represents one drag data item.</span></span> |
| <span data-ttu-id="17c29-217">Chyba</span><span class="sxs-lookup"><span data-stu-id="17c29-217">Error</span></span> | `UIErrorEventArgs` |
| <span data-ttu-id="17c29-218">fokus</span><span class="sxs-lookup"><span data-stu-id="17c29-218">Focus</span></span> | <span data-ttu-id="17c29-219">`UIFocusEventArgs` &ndash; Neobsahuje podporu pro `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="17c29-219">`UIFocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="17c29-220">`<input>` Změna</span><span class="sxs-lookup"><span data-stu-id="17c29-220">`<input>` change</span></span> | `UIChangeEventArgs` |
| <span data-ttu-id="17c29-221">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="17c29-221">Keyboard</span></span> | `UIKeyboardEventArgs` |
| <span data-ttu-id="17c29-222">Myši</span><span class="sxs-lookup"><span data-stu-id="17c29-222">Mouse</span></span> | `UIMouseEventArgs` |
| <span data-ttu-id="17c29-223">Ukazatele myši</span><span class="sxs-lookup"><span data-stu-id="17c29-223">Mouse pointer</span></span> | `UIPointerEventArgs` |
| <span data-ttu-id="17c29-224">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="17c29-224">Mouse wheel</span></span> | `UIWheelEventArgs` |
| <span data-ttu-id="17c29-225">Průběh</span><span class="sxs-lookup"><span data-stu-id="17c29-225">Progress</span></span> | `UIProgressEventArgs` |
| <span data-ttu-id="17c29-226">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="17c29-226">Touch</span></span> | <span data-ttu-id="17c29-227">`UITouchEventArgs` &ndash; `UITouchPoint` představuje jediné kontaktní místo na dotyk zařízení.</span><span class="sxs-lookup"><span data-stu-id="17c29-227">`UITouchEventArgs` &ndash; `UITouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="17c29-228">Informace o vlastnostech a zpracování událostí v předchozí tabulce chování událostí najdete v tématu [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) ve zdroji odkazu.</span><span class="sxs-lookup"><span data-stu-id="17c29-228">For information on the properties and event handling behavior of the events in the preceding table, see [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) in the reference source.</span></span>
  
<span data-ttu-id="17c29-229">Výrazy lambda lze také použít:</span><span class="sxs-lookup"><span data-stu-id="17c29-229">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="17c29-230">Často je vhodné zavřít další hodnoty, například během iterace přes sadu elementů.</span><span class="sxs-lookup"><span data-stu-id="17c29-230">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="17c29-231">Následující příklad vytvoří tři tlačítka, každý z který volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a tlačítko (`buttonNumber`) při výběru v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="17c29-231">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="17c29-232">Proveďte **není** použít proměnnou smyčky (`i`) v `for` smyčky přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="17c29-232">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="17c29-233">V opačném případě se používá stejnou proměnnou všechny výrazy lambda způsobí `i`hodnotu být stejné ve všech výrazů lambda.</span><span class="sxs-lookup"><span data-stu-id="17c29-233">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="17c29-234">Zachytit její hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak přes ni.</span><span class="sxs-lookup"><span data-stu-id="17c29-234">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="17c29-235">EventCallback</span><span class="sxs-lookup"><span data-stu-id="17c29-235">EventCallback</span></span>

<span data-ttu-id="17c29-236">Je běžným scénářem vnořených součástí chce spouštět nadřazenou metodu komponenty, při výskytu události podřízené součásti&mdash;například, když `onclick` v podřízeném dojde k události.</span><span class="sxs-lookup"><span data-stu-id="17c29-236">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="17c29-237">Chcete-li zpřístupnit událostí mezi komponentami, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="17c29-237">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="17c29-238">Nadřazené komponenty můžete přiřadit metody zpětného volání k podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="17c29-238">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="17c29-239">`ChildComponent` v ukázce aplikace předvádí, jak tlačítka `onclick` přijímat je nastavena obslužná rutina `EventCallback` delegovat z tohoto příkladu `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="17c29-239">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="17c29-240">`EventCallback` Je zadán s `UIMouseEventArgs`, což je vhodné pro `onclick` událost z periferní zařízení:</span><span class="sxs-lookup"><span data-stu-id="17c29-240">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="17c29-241">`ParentComponent` Nastaví dítěte `EventCallback<T>` k jeho `ShowMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="17c29-241">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="17c29-242">Při výběru tlačítka `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="17c29-242">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="17c29-243">`ParentComponent`Společnosti `ShowMessage` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="17c29-243">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="17c29-244">`messageText` se aktualizují a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="17c29-244">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="17c29-245">Volání `StateHasChanged` není nutné v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="17c29-245">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="17c29-246">`StateHasChanged` je volána automaticky k rerender `ParentComponent`, stejně jako podřízené události aktivovat součást rerendering v obslužných rutinách událostí, které jsou spuštěny v rámci podřízené.</span><span class="sxs-lookup"><span data-stu-id="17c29-246">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="17c29-247">`EventCallback` a `EventCallback<T>` povolit asynchronních delegátů.</span><span class="sxs-lookup"><span data-stu-id="17c29-247">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="17c29-248">`EventCallback<T>` silně typované a vyžaduje konkrétní argument typu.</span><span class="sxs-lookup"><span data-stu-id="17c29-248">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="17c29-249">`EventCallback` slabě typované a jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-249">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="17c29-250">Vyvolání `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a operátoru await <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="17c29-250">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="17c29-251">Použití `EventCallback` a `EventCallback<T>` pro událost zpracování a vazby parametrů komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-251">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="17c29-252">Preferovat silného typu `EventCallback<T>` přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="17c29-252">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="17c29-253">`EventCallback<T>` poskytuje lepší zpětnou vazbu chyba uživatelům komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-253">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="17c29-254">Podobně jako jiné obslužné rutině události uživatelského rozhraní, zadání parametru události je volitelné.</span><span class="sxs-lookup"><span data-stu-id="17c29-254">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="17c29-255">Použití `EventCallback` při neexistuje převáděná hodnota předaný zpětnému volání.</span><span class="sxs-lookup"><span data-stu-id="17c29-255">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="17c29-256">Zachycení odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="17c29-256">Capture references to components</span></span>

<span data-ttu-id="17c29-257">Komponenta odkazy poskytují způsob, jak odkazovat na instanci komponenty tak, že můžete použít příkazy do této instance, jako například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="17c29-257">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="17c29-258">Chcete-li zachytit odkazem komponenty, přidejte `@ref` atribut podřízené součásti a pak definovat pole se stejným názvem a stejného typu jako podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="17c29-258">To capture a component reference, add a `@ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="17c29-259">Při vykreslování komponentu `loginDialog` pole se vyplní `MyLoginDialog` podřízené instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-259">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="17c29-260">Potom můžete vyvolat metody rozhraní .NET na instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-260">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="17c29-261">`loginDialog` Proměnná je vyplněný pouze poté, co se vykreslí komponentu a zahrnuje její výstup `MyLoginDialog` elementu.</span><span class="sxs-lookup"><span data-stu-id="17c29-261">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="17c29-262">Až do bodu není nutné nic odkazovat.</span><span class="sxs-lookup"><span data-stu-id="17c29-262">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="17c29-263">K manipulaci s odkazy na součásti po dokončení vykreslení komponentu, použijte `OnAfterRenderAsync` nebo `OnAfterRender` metody.</span><span class="sxs-lookup"><span data-stu-id="17c29-263">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="17c29-264">Při zachytávání odkazů na komponenty pomocí syntaxe podobné [zachytávání odkazy na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) funkce.</span><span class="sxs-lookup"><span data-stu-id="17c29-264">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="17c29-265">Nejsou součástí odkazy předané do kódu jazyka JavaScript&mdash;se použít jenom v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="17c29-265">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="17c29-266">Proveďte **není** mutovat stavu podřízenými komponentami pomocí odkazů na komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-266">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="17c29-267">Místo toho použijte normální deklarované parametry k předání dat podřízenými komponentami.</span><span class="sxs-lookup"><span data-stu-id="17c29-267">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="17c29-268">Použijte normální deklarované parametry výsledku v podřízené součásti, které automaticky rerender ve správném čase.</span><span class="sxs-lookup"><span data-stu-id="17c29-268">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="17c29-269">Použití @key řídit zachování prvky a součásti</span><span class="sxs-lookup"><span data-stu-id="17c29-269">Use @key to control the preservation of elements and components</span></span>

<span data-ttu-id="17c29-270">Při vykreslování seznamu elementů nebo komponenty prvky a součásti následně změnit, algoritmus rozdílování Blazor společnosti musíte rozhodnout, které z předchozí prvky nebo komponent se můžou ukládat a mapování objektů modelu do nich.</span><span class="sxs-lookup"><span data-stu-id="17c29-270">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="17c29-271">Za normálních okolností tento proces probíhá automaticky a můžete ignorovat, ale existují případy, kde můžete chtít řízení procesu.</span><span class="sxs-lookup"><span data-stu-id="17c29-271">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="17c29-272">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="17c29-272">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="17c29-273">Obsah `People` kolekce může změnit s vložit, odstranit nebo měnit pořadí položek.</span><span class="sxs-lookup"><span data-stu-id="17c29-273">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="17c29-274">Když rerenders komponentu `<DetailsEditor>` komponenta může změnit na jiný `Details` hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="17c29-274">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="17c29-275">To může způsobit složitější rerendering, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="17c29-275">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="17c29-276">V některých případech rerendering může vést k rozdíly v chování viditelné, například ke ztrátě prvek fokus.</span><span class="sxs-lookup"><span data-stu-id="17c29-276">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="17c29-277">Proces mapování se dá řídit pomocí `@key` atribut direktivy.</span><span class="sxs-lookup"><span data-stu-id="17c29-277">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="17c29-278">`@key` způsobí, že algoritmus rozdílování zaručit zachování elementy nebo komponenty založené na hodnotě klíče:</span><span class="sxs-lookup"><span data-stu-id="17c29-278">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    private IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="17c29-279">Když `People` změny kolekce algoritmus rozdílování uchovává přidružení mezi `<DetailsEditor>` instancí a `person` instancí:</span><span class="sxs-lookup"><span data-stu-id="17c29-279">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="17c29-280">Pokud `Person` je odstraněn z `People` seznamu pouze odpovídající `<DetailsEditor>` instance je odebrána z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="17c29-280">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="17c29-281">Jindy jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="17c29-281">Other instances are left unchanged.</span></span>
* <span data-ttu-id="17c29-282">Pokud `Person` se vloží některé pozici v seznamu, jeden nové `<DetailsEditor>` instance bude vložena na této pozici odpovídající.</span><span class="sxs-lookup"><span data-stu-id="17c29-282">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="17c29-283">Jindy jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="17c29-283">Other instances are left unchanged.</span></span>
* <span data-ttu-id="17c29-284">Pokud `Person` jsou položky měnit pořadí, odpovídající `<DetailsEditor>` instancí jsou zachovány a pořadí v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="17c29-284">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="17c29-285">V některých scénářích použití `@key` minimalizuje rerendering složitost a potenciální problémy s stavové části modelu DOM změna například pozici fokus se vyhnete.</span><span class="sxs-lookup"><span data-stu-id="17c29-285">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="17c29-286">Klíče jsou místní pro každý kontejner prvku nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-286">Keys are local to each container element or component.</span></span> <span data-ttu-id="17c29-287">Klíče nejsou porovnávány globálně v dokumentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-287">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="17c29-288">Kdy použít @key</span><span class="sxs-lookup"><span data-stu-id="17c29-288">When to use @key</span></span>

<span data-ttu-id="17c29-289">Obvykle je vhodné použít `@key` vždy, když je vykreslen seznamu (například v `@foreach` bloku) a k definování existuje vhodnou hodnotu `@key`.</span><span class="sxs-lookup"><span data-stu-id="17c29-289">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="17c29-290">Můžete také použít `@key` zabránit Blazor zachování prvku nebo komponenty podstrom, když se změní objekt:</span><span class="sxs-lookup"><span data-stu-id="17c29-290">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="17c29-291">Pokud `@currentPerson` změny, `@key` atribut direktivy vynutí Blazor zahodíte celý `<div>` a její potomci a znovu sestavte podstrom uživatelského rozhraní pomocí nové prvky a součásti.</span><span class="sxs-lookup"><span data-stu-id="17c29-291">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="17c29-292">To může být užitečné, pokud potřebujete zajistit, že žádný stav uživatelského rozhraní se zachová při `@currentPerson` změny.</span><span class="sxs-lookup"><span data-stu-id="17c29-292">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="17c29-293">Kdy nepoužívat @key</span><span class="sxs-lookup"><span data-stu-id="17c29-293">When not to use @key</span></span>

<span data-ttu-id="17c29-294">Výkon, když je rozdílování s `@key`.</span><span class="sxs-lookup"><span data-stu-id="17c29-294">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="17c29-295">Není velké náklady na výkon, ale pouze zadat `@key` Pokud řízení zachování prvku nebo komponenty pravidla využívat aplikace.</span><span class="sxs-lookup"><span data-stu-id="17c29-295">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="17c29-296">I když `@key` není použit, Blazor zachová podřízený element a součást instance co největší míře.</span><span class="sxs-lookup"><span data-stu-id="17c29-296">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="17c29-297">Jedinou výhodou používání `@key` je ovládací prvek průběhu *jak* instancí modelu jsou mapovány na instance zachovaných součástí, namísto rozdílování algoritmus výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="17c29-297">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="17c29-298">Jaké hodnoty je třeba použít pro @key</span><span class="sxs-lookup"><span data-stu-id="17c29-298">What values to use for @key</span></span>

<span data-ttu-id="17c29-299">Obecně je vhodné jej zadejte hodnoty pro následující typy `@key`:</span><span class="sxs-lookup"><span data-stu-id="17c29-299">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="17c29-300">Model instance objektů (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="17c29-300">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="17c29-301">Tím se zajistí zachování založené na objektu referenční rovnost.</span><span class="sxs-lookup"><span data-stu-id="17c29-301">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="17c29-302">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`, nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="17c29-302">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="17c29-303">Vyhněte se zadání hodnoty, které mohou kolidovat neočekávaně.</span><span class="sxs-lookup"><span data-stu-id="17c29-303">Avoid supplying a value that can clash unexpectedly.</span></span> <span data-ttu-id="17c29-304">Pokud `@key="@someObject.GetHashCode()"` pochází, může dojít k neočekávaným došlo ke konfliktům protože kódů hash objektů nesouvisejících může být stejný.</span><span class="sxs-lookup"><span data-stu-id="17c29-304">If `@key="@someObject.GetHashCode()"` is supplied, unexpected clashes may occur because the hash codes of unrelated objects can be the same.</span></span> <span data-ttu-id="17c29-305">Pokud se kolidující `@key` hodnoty jsou požadovány v rámci stejné nadřazené `@key` nebude respektovat hodnoty.</span><span class="sxs-lookup"><span data-stu-id="17c29-305">If clashing `@key` values are requested within the same parent, the `@key` values won't be honored.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="17c29-306">Životní cyklus metody</span><span class="sxs-lookup"><span data-stu-id="17c29-306">Lifecycle methods</span></span>

<span data-ttu-id="17c29-307">`OnInitAsync` a `OnInit` spouštění kódu se inicializovat komponentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-307">`OnInitAsync` and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="17c29-308">Chcete-li provádění asynchronní operace, použijte `OnInitAsync` a `await` – klíčové slovo v operaci:</span><span class="sxs-lookup"><span data-stu-id="17c29-308">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="17c29-309">Synchronní operace, použijte `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="17c29-309">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="17c29-310">`OnParametersSetAsync` a `OnParametersSet` se volá, když součást přijme parametry ze svého nadřazeného objektu a hodnoty jsou přiřazeny k vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="17c29-310">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="17c29-311">Tyto metody jsou provedeny po inicializaci součásti a pokaždé, když je vykreslen komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-311">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="17c29-312">`OnAfterRenderAsync` a `OnAfterRender` se volá se po vykreslení komponentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-312">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="17c29-313">V tomto okamžiku se vyplní elementu a součást odkazy.</span><span class="sxs-lookup"><span data-stu-id="17c29-313">Element and component references are populated at this point.</span></span> <span data-ttu-id="17c29-314">Pomocí této fázi můžete provést další inicializaci postup pomocí vykreslovaný obsah, jako je aktivace JavaScript knihovny třetích stran, které pracují s vykreslené elementy modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="17c29-314">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="17c29-315">Zpracování neúplné asynchronní akce v vykreslení</span><span class="sxs-lookup"><span data-stu-id="17c29-315">Handle incomplete async actions at render</span></span>

<span data-ttu-id="17c29-316">Asynchronní akce prováděné v události životního cyklu pravděpodobně nebyly dokončeny před vykreslením komponentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-316">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="17c29-317">Objekty mohou být `null` nebo neúplně naplněný daty při provádění metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="17c29-317">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="17c29-318">Poskytnout logiku vykreslování potvrďte, že objekty jsou inicializovány.</span><span class="sxs-lookup"><span data-stu-id="17c29-318">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="17c29-319">Vykreslení zástupné prvky uživatelského rozhraní (například načítání zpráv) při objekty jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="17c29-319">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="17c29-320">V `FetchData` součást šablony Blazor `OnInitAsync` je potlačena za účelem nekopírovat přijímat data předpovědi (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="17c29-320">In the `FetchData` component of the Blazor templates, `OnInitAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="17c29-321">Když `forecasts` je `null`, uživateli se zobrazí zpráva načtení.</span><span class="sxs-lookup"><span data-stu-id="17c29-321">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="17c29-322">Po `Task` vrácený `OnInitAsync` dokončí, komponenta je rerendered s aktualizovaný stav.</span><span class="sxs-lookup"><span data-stu-id="17c29-322">After the `Task` returned by `OnInitAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="17c29-323">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="17c29-323">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="17c29-324">Spuštění kódu před parametry jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="17c29-324">Execute code before parameters are set</span></span>

<span data-ttu-id="17c29-325">`SetParameters` může být potlačena za účelem spouštění kódu předtím, než jsou nastavené parametry:</span><span class="sxs-lookup"><span data-stu-id="17c29-325">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="17c29-326">Pokud `base.SetParameters` není vyvolána, můžete vlastní kód interpretaci příchozí hodnoty parametrů v jakékoli požadované stejně, jako.</span><span class="sxs-lookup"><span data-stu-id="17c29-326">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="17c29-327">Například příchozí parametry nejsou potřeba přiřadit k vlastnosti ve třídě.</span><span class="sxs-lookup"><span data-stu-id="17c29-327">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="17c29-328">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="17c29-328">Suppress refreshing of the UI</span></span>

<span data-ttu-id="17c29-329">`ShouldRender` může být potlačena za účelem potlačit aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="17c29-329">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="17c29-330">Implementace vrátí-li `true`, aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="17c29-330">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="17c29-331">I když `ShouldRender` je přepsána, komponenta je vždy Počáteční vykreslení.</span><span class="sxs-lookup"><span data-stu-id="17c29-331">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="17c29-332">Vyřazení komponenty pomocí rozhraní IDisposable</span><span class="sxs-lookup"><span data-stu-id="17c29-332">Component disposal with IDisposable</span></span>

<span data-ttu-id="17c29-333">Pokud komponenta implementuje <xref:System.IDisposable>, [metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) se volá, když je součást z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="17c29-333">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="17c29-334">Používá následující komponenty `@implements IDisposable` a `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="17c29-334">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="17c29-335">Směrování</span><span class="sxs-lookup"><span data-stu-id="17c29-335">Routing</span></span>

<span data-ttu-id="17c29-336">Směrování v Blazor se dosahuje tím, že poskytuje šablona trasy pro jednotlivé dostupné komponenty v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="17c29-336">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="17c29-337">Když soubor Razor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="17c29-337">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="17c29-338">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="17c29-338">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="17c29-339">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-339">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="17c29-340">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="17c29-340">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="17c29-341">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="17c29-341">Route parameters</span></span>

<span data-ttu-id="17c29-342">Součásti mohou přijímat parametry trasy z šablonu trasy, které jsou součástí `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="17c29-342">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="17c29-343">Směrovač používá parametry trasy k naplnění odpovídající komponenta parametry.</span><span class="sxs-lookup"><span data-stu-id="17c29-343">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="17c29-344">*Komponenta parametr trasa*:</span><span class="sxs-lookup"><span data-stu-id="17c29-344">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="17c29-345">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="17c29-345">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="17c29-346">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="17c29-346">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="17c29-347">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="17c29-347">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="17c29-348">Základní třída dědičnosti "použití modelu code-behind" prostředí</span><span class="sxs-lookup"><span data-stu-id="17c29-348">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="17c29-349">Soubory součástí kombinovat kód HTML a C# zpracování kódu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="17c29-349">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="17c29-350">`@inherits` – Direktiva je možné poskytnout Blazor aplikace, který odděluje komponenty značek z kódu pro zpracování prostředí "použití modelu code-behind".</span><span class="sxs-lookup"><span data-stu-id="17c29-350">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="17c29-351">[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak komponenty může dědit základní třídy `BlazorRocksBase`, aby vznikl komponenty vlastnosti a metody.</span><span class="sxs-lookup"><span data-stu-id="17c29-351">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="17c29-352">*Pages/BlazorRocks.razor*:</span><span class="sxs-lookup"><span data-stu-id="17c29-352">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="17c29-353">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="17c29-353">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="17c29-354">Základní třída musí být odvozený z: `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="17c29-354">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="17c29-355">Importovat komponenty</span><span class="sxs-lookup"><span data-stu-id="17c29-355">Import components</span></span>

<span data-ttu-id="17c29-356">Obor názvů součásti zleva doprava Razor podle:</span><span class="sxs-lookup"><span data-stu-id="17c29-356">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="17c29-357">V projektu `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="17c29-357">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="17c29-358">Cesta z kořenového adresáře projektu do komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-358">The path from the project root to the component.</span></span> <span data-ttu-id="17c29-359">Například `ComponentsSample/Pages/Index.razor` je v oboru názvů `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="17c29-359">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="17c29-360">Postupujte podle součásti C# název pravidel vazby.</span><span class="sxs-lookup"><span data-stu-id="17c29-360">Components follow C# name binding rules.</span></span> <span data-ttu-id="17c29-361">V případě třídy *Index.razor*, všechny součásti ve stejné složce *stránky*a nadřazené složky *ComponentsSample*, jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="17c29-361">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="17c29-362">Součásti definované v jiný obor názvů může být přenesena do rozsahu pomocí syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice.</span><span class="sxs-lookup"><span data-stu-id="17c29-362">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="17c29-363">Pokud jiná komponenta, `NavMenu.razor`, existuje ve složce `ComponentsSample/Shared/`, součást je možné v `Index.razor` následujícím `@using` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="17c29-363">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="17c29-364">Součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, která eliminuje potřebu [ \@pomocí](xref:mvc/views/razor#using) – direktiva:</span><span class="sxs-lookup"><span data-stu-id="17c29-364">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="17c29-365">`global::` Kvalifikace se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="17c29-365">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="17c29-366">Import součásti s aliasem `using` příkazy (například `@using Foo = Bar`) se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="17c29-366">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="17c29-367">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="17c29-367">Partially qualified names aren't supported.</span></span> <span data-ttu-id="17c29-368">Například přidáním `@using ComponentsSample` a odkazování na ně `NavMenu.razor` s `<Shared.NavMenu></Shared.NavMenu>` se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="17c29-368">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="razor-support"></a><span data-ttu-id="17c29-369">Podpora Razor</span><span class="sxs-lookup"><span data-stu-id="17c29-369">Razor support</span></span>

<span data-ttu-id="17c29-370">**Direktivy Razor**</span><span class="sxs-lookup"><span data-stu-id="17c29-370">**Razor directives**</span></span>

<span data-ttu-id="17c29-371">V následující tabulce jsou uvedeny direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="17c29-371">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="17c29-372">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="17c29-372">Directive</span></span> | <span data-ttu-id="17c29-373">Popis</span><span class="sxs-lookup"><span data-stu-id="17c29-373">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="17c29-374">\@code</span><span class="sxs-lookup"><span data-stu-id="17c29-374">\@code</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="17c29-375">Přidá C# blok kódu na komponentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-375">Adds a C# code block to a component.</span></span> <span data-ttu-id="17c29-376">`@code` je alias pro `@functions`.</span><span class="sxs-lookup"><span data-stu-id="17c29-376">`@code` is an alias of `@functions`.</span></span> <span data-ttu-id="17c29-377">`@code` doporučuje se přes `@functions`.</span><span class="sxs-lookup"><span data-stu-id="17c29-377">`@code` is recommended over `@functions`.</span></span> <span data-ttu-id="17c29-378">Více než jeden `@code` blok je povolený.</span><span class="sxs-lookup"><span data-stu-id="17c29-378">More than one `@code` block is permissible.</span></span> |
| [<span data-ttu-id="17c29-379">\@Funkce</span><span class="sxs-lookup"><span data-stu-id="17c29-379">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="17c29-380">Přidá C# blok kódu na komponentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-380">Adds a C# code block to a component.</span></span> <span data-ttu-id="17c29-381">Zvolte `@code` přes `@functions` pro C# bloky kódu.</span><span class="sxs-lookup"><span data-stu-id="17c29-381">Choose `@code` over `@functions` for C# code blocks.</span></span> |
| `@implements` | <span data-ttu-id="17c29-382">Implementuje rozhraní pro třídu vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-382">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="17c29-383">\@inherits</span><span class="sxs-lookup"><span data-stu-id="17c29-383">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="17c29-384">Poskytuje plnou kontrolu nad třídu, která dědí komponentu.</span><span class="sxs-lookup"><span data-stu-id="17c29-384">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="17c29-385">\@Vložení</span><span class="sxs-lookup"><span data-stu-id="17c29-385">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="17c29-386">Vkládání ze služby umožňuje [kontejneru služby](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="17c29-386">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="17c29-387">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="17c29-387">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="17c29-388">Určuje komponentu rozložení.</span><span class="sxs-lookup"><span data-stu-id="17c29-388">Specifies a layout component.</span></span> <span data-ttu-id="17c29-389">Rozložení komponenty umožňují zabránit zdvojení kódu a nekonzistence.</span><span class="sxs-lookup"><span data-stu-id="17c29-389">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="17c29-390">\@Stránka</span><span class="sxs-lookup"><span data-stu-id="17c29-390">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="17c29-391">Určuje, že by měla komponenta zpracování požadavků přímo.</span><span class="sxs-lookup"><span data-stu-id="17c29-391">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="17c29-392">`@page` – Direktiva je možné zadat při trasy a volitelné parametry.</span><span class="sxs-lookup"><span data-stu-id="17c29-392">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="17c29-393">Na rozdíl od Razor Pages `@page` – direktiva nemusí být první – direktiva v horní části souboru.</span><span class="sxs-lookup"><span data-stu-id="17c29-393">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="17c29-394">Další informace najdete v tématu [směrování](xref:blazor/routing).</span><span class="sxs-lookup"><span data-stu-id="17c29-394">For more information, see [Routing](xref:blazor/routing).</span></span> |
| [<span data-ttu-id="17c29-395">\@použití</span><span class="sxs-lookup"><span data-stu-id="17c29-395">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="17c29-396">Přidá C# `using` směrnice do třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-396">Adds the C# `using` directive to the generated component class.</span></span> <span data-ttu-id="17c29-397">To přináší také všechny součásti, které jsou definované v tomto oboru názvů do oboru.</span><span class="sxs-lookup"><span data-stu-id="17c29-397">This also brings all the components defined in that namespace into scope.</span></span> |
| [<span data-ttu-id="17c29-398">\@namespace</span><span class="sxs-lookup"><span data-stu-id="17c29-398">\@namespace</span></span>](xref:mvc/views/razor#section-6) | <span data-ttu-id="17c29-399">Nastaví obor názvů, třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-399">Sets the namespace of the generated component class.</span></span> |
| [<span data-ttu-id="17c29-400">\@attribute</span><span class="sxs-lookup"><span data-stu-id="17c29-400">\@attribute</span></span>](xref:mvc/views/razor#section-7) | <span data-ttu-id="17c29-401">Atribut se přidá do třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-401">Adds an attribute to the generated component class.</span></span> |

<span data-ttu-id="17c29-402">**Podmíněné atributy prvků HTML**</span><span class="sxs-lookup"><span data-stu-id="17c29-402">**Conditional HTML element attributes**</span></span>

<span data-ttu-id="17c29-403">Atributy prvků HTML jsou vykreslovány podmíněně na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="17c29-403">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="17c29-404">Pokud je hodnota `false` nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="17c29-404">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="17c29-405">Pokud je hodnota `true`, atribut je vykreslen minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="17c29-405">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="17c29-406">V následujícím příkladu `IsCompleted` Určuje, zda `checked` se vykreslí v elementu značky:</span><span class="sxs-lookup"><span data-stu-id="17c29-406">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="17c29-407">Pokud `IsCompleted` je `true`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="17c29-407">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="17c29-408">Pokud `IsCompleted` je `false`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="17c29-408">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="17c29-409">**Další informace o syntaxi Razor**</span><span class="sxs-lookup"><span data-stu-id="17c29-409">**Additional information on Razor**</span></span>

<span data-ttu-id="17c29-410">Další informace o syntaxi Razor, najdete v článku [referenční příručka syntaxe Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="17c29-410">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="17c29-411">Raw HTML</span><span class="sxs-lookup"><span data-stu-id="17c29-411">Raw HTML</span></span>

<span data-ttu-id="17c29-412">Řetězce jsou obvykle vykreslen pomocí modelu DOM textové uzly, což znamená, že všechny značky, které mohou obsahovat je ignorována a považována jako prostý text.</span><span class="sxs-lookup"><span data-stu-id="17c29-412">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="17c29-413">Pokud chcete zobrazit nezpracovaný kód HTML, zabalit obsah HTML v `MarkupString` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="17c29-413">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="17c29-414">Hodnota je analyzovat ve formátu HTML nebo SVG a vložit do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="17c29-414">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="17c29-415">Vykreslování nezpracovaný kód HTML vytvořený z libovolného nedůvěryhodný zdroj je **bezpečnostní riziko** a mělo by se vyhnout!</span><span class="sxs-lookup"><span data-stu-id="17c29-415">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="17c29-416">Následující příklad ukazuje použití `MarkupString` typu přidáte blok statického obsahu HTML vykresleného výstupu součásti:</span><span class="sxs-lookup"><span data-stu-id="17c29-416">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="17c29-417">Bez vizuálního vzhledu součásti</span><span class="sxs-lookup"><span data-stu-id="17c29-417">Templated components</span></span>

<span data-ttu-id="17c29-418">Bez vizuálního vzhledu komponenty jsou součástí, které přijímají jeden nebo více šablon uživatelského rozhraní jako parametry, které lze použít jako součást logiky komponenty vykreslování.</span><span class="sxs-lookup"><span data-stu-id="17c29-418">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="17c29-419">Bez vizuálního vzhledu komponenty umožňují vytvářet vyšší úrovně součásti, které jsou více než regulární komponenty opakovaně použitelné.</span><span class="sxs-lookup"><span data-stu-id="17c29-419">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="17c29-420">Zahrnují několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="17c29-420">A couple of examples include:</span></span>

* <span data-ttu-id="17c29-421">Komponenta tabulky, která umožňuje uživateli zadat šablony pro záhlaví tabulky, řádky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="17c29-421">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="17c29-422">Seznam součástí, která umožňuje uživateli zadat šablonu pro vykreslování položky v seznamu.</span><span class="sxs-lookup"><span data-stu-id="17c29-422">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="17c29-423">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="17c29-423">Template parameters</span></span>

<span data-ttu-id="17c29-424">Bez vizuálního vzhledu součásti je definován tak, že zadáte jeden nebo více parametrů součást typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="17c29-424">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="17c29-425">Vykreslení fragment reprezentuje segment, který uživatelského rozhraní, které je vykresleno komponentou.</span><span class="sxs-lookup"><span data-stu-id="17c29-425">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="17c29-426">Vykreslení fragment volitelně přebírá parametr, který lze zadat, pokud je vyvolána fragment vykreslení.</span><span class="sxs-lookup"><span data-stu-id="17c29-426">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="17c29-427">`TableTemplate` Komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-427">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="17c29-428">Při použití komponenty bez vizuálního vzhledu, parametry šablony lze pomocí podřízené prvky, které odpovídají názvům parametry (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="17c29-428">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="17c29-429">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="17c29-429">Template context parameters</span></span>

<span data-ttu-id="17c29-430">Komponenta argumenty typu `RenderFragment<T>` předaný jako elementy mají implicitní parametr s názvem `context` (například z předchozí příklad kódu `@context.PetId`), ale můžete změnit pomocí parametru název `Context` atribut na podřízené element.</span><span class="sxs-lookup"><span data-stu-id="17c29-430">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="17c29-431">V následujícím příkladu `RowTemplate` elementu `Context` Určuje atribut `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="17c29-431">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="17c29-432">Alternativně můžete zadat `Context` atribut na prvek součásti.</span><span class="sxs-lookup"><span data-stu-id="17c29-432">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="17c29-433">Zadaný `Context` atributu platí pro všechny parametry určené šablony.</span><span class="sxs-lookup"><span data-stu-id="17c29-433">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="17c29-434">To může být užitečné, pokud chcete zadat název obsahu parametru pro implicitní podřízený obsah (bez jakékoli zabalení podřízený element).</span><span class="sxs-lookup"><span data-stu-id="17c29-434">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="17c29-435">V následujícím příkladu `Context` atributu se zobrazí na `TableTemplate` elementu a platí pro všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="17c29-435">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="17c29-436">Obecné typy komponenty</span><span class="sxs-lookup"><span data-stu-id="17c29-436">Generic-typed components</span></span>

<span data-ttu-id="17c29-437">Bez vizuálního vzhledu součásti jsou často obecně typu.</span><span class="sxs-lookup"><span data-stu-id="17c29-437">Templated components are often generically typed.</span></span> <span data-ttu-id="17c29-438">Například obecný `ListViewTemplate` komponenty lze použít k vykreslení `IEnumerable<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="17c29-438">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="17c29-439">K definování obecné součásti, použijte `@typeparam` směrnice a určete parametry typu:</span><span class="sxs-lookup"><span data-stu-id="17c29-439">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="17c29-440">Při použití komponenty obecného typu, parametr typu je odvozený Pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="17c29-440">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="17c29-441">V opačném případě parametr typu musí být explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="17c29-441">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="17c29-442">V následujícím příkladu `TItem="Pet"` Určuje typ:</span><span class="sxs-lookup"><span data-stu-id="17c29-442">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="17c29-443">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="17c29-443">Cascading values and parameters</span></span>

<span data-ttu-id="17c29-444">V některých případech je nevhodné toku dat z jako součást nadřazené komponenty potomka pomocí [parametry komponenty](#component-parameters), zvlášť pokud máte několik vrstev komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-444">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="17c29-445">Kaskádové hodnoty a parametry tento problém vyřešit tím, že poskytuje pohodlný způsob pro komponentu předchůdce k zadání hodnoty pro všechny jeho podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="17c29-445">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="17c29-446">Kaskádové hodnoty a parametry také poskytnout přístup pro součásti pro koordinaci.</span><span class="sxs-lookup"><span data-stu-id="17c29-446">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="17c29-447">Příklad motiv</span><span class="sxs-lookup"><span data-stu-id="17c29-447">Theme example</span></span>

<span data-ttu-id="17c29-448">V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivech tok součástí hierarchií směrem dolů, aby všechna tlačítka v rámci dané části aplikace sdílet stejný styl.</span><span class="sxs-lookup"><span data-stu-id="17c29-448">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="17c29-449">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="17c29-449">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="17c29-450">Jako součást předchůdce může poskytnout kaskádové hodnotu pomocí komponenty kaskádové hodnotu.</span><span class="sxs-lookup"><span data-stu-id="17c29-450">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="17c29-451">`CascadingValue` Komponenty zabalí podstrom součástí hierarchie a poskytuje jednu hodnotu pro všechny komponenty v rámci této podstrom.</span><span class="sxs-lookup"><span data-stu-id="17c29-451">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="17c29-452">Například ukázkové aplikace určuje informace o motivech (`ThemeInfo`) v jednom z rozložení aplikace jako parametr šablony pro všechny součásti, které tvoří rozložení textu `@Body` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="17c29-452">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="17c29-453">`ButtonClass` je přiřazena hodnota `btn-success` v komponentě rozložení.</span><span class="sxs-lookup"><span data-stu-id="17c29-453">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="17c29-454">Všechny podřízené součásti mohou využívat tuto vlastnost prostřednictvím `ThemeInfo` šablony objektu.</span><span class="sxs-lookup"><span data-stu-id="17c29-454">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="17c29-455">`CascadingValuesParametersLayout` Komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-455">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="17c29-456">Chcete-li pomocí kaskádových hodnot, komponenty deklarovat kaskádové parametry s využitím `[CascadingParameter]` atribut nebo na základě hodnoty názvu řetězec:</span><span class="sxs-lookup"><span data-stu-id="17c29-456">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="17c29-457">Vazba s hodnotou řetězce názvu platí, pokud máte více kaskádových hodnot stejného typu a pro jejich odlišení v rámci stejné podstrom.</span><span class="sxs-lookup"><span data-stu-id="17c29-457">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="17c29-458">Kaskádové hodnoty se váží k parametrům šablony podle typu.</span><span class="sxs-lookup"><span data-stu-id="17c29-458">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="17c29-459">V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta vytvoří vazbu `ThemeInfo` kaskádové hodnotu pro parametr šablony.</span><span class="sxs-lookup"><span data-stu-id="17c29-459">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="17c29-460">Tento parametr se používá nastavení třídy šablony stylů CSS pro jedno z tlačítek zobrazí komponentou.</span><span class="sxs-lookup"><span data-stu-id="17c29-460">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="17c29-461">`CascadingValuesParametersTheme` Komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-461">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a><span data-ttu-id="17c29-462">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="17c29-462">TabSet example</span></span>

<span data-ttu-id="17c29-463">Parametry šablony také povolit součásti spolupracovat napříč hierarchií komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-463">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="17c29-464">Představte si třeba následující *TabSet* příklad v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="17c29-464">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="17c29-465">Ukázková aplikace má `ITab` rozhraní, které karty implementace:</span><span class="sxs-lookup"><span data-stu-id="17c29-465">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="17c29-466">`CascadingValuesParametersTabSet` Komponenta používá `TabSet` komponenta, která obsahuje několik `Tab` komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-466">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="17c29-467">Podřízené `Tab` součásti nejsou explicitně předány jako parametry `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="17c29-467">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="17c29-468">Místo toho podřízené `Tab` komponenty jsou součástí podřízený obsah `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="17c29-468">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="17c29-469">Ale `TabSet` stále je potřeba vědět o každém `Tab` komponentu tak, aby ho může mít za následek záhlaví a na aktivní kartě. Povolit koordinace bez potřeby dalšího kódu, `TabSet` komponenty *samotný můžete zadat jako hodnotu kaskádové* , který pak převezme potomka `Tab` komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-469">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="17c29-470">`TabSet` Komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-470">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="17c29-471">Potomka `Tab` součásti capture obsahující `TabSet` jako parametr šablony, proto `Tab` součásti přidat samy o sobě `TabSet` a souřadnice, na které kartě je aktivní.</span><span class="sxs-lookup"><span data-stu-id="17c29-471">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="17c29-472">`Tab` Komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-472">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="17c29-473">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="17c29-473">Razor templates</span></span>

<span data-ttu-id="17c29-474">Vykreslení fragmenty lze definovat pomocí syntaxe šablon Razor.</span><span class="sxs-lookup"><span data-stu-id="17c29-474">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="17c29-475">Šablony Razor představují způsob, jak definovat fragment kódu uživatelského rozhraní a předpokládají následující formát:</span><span class="sxs-lookup"><span data-stu-id="17c29-475">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="17c29-476">Následující příklad ukazuje, jak určit `RenderFragment` a `RenderFragment<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="17c29-476">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="17c29-477">`RazorTemplates` Komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-477">`RazorTemplates` component:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="17c29-478">Vykreslení fragmenty definované pomocí syntaxe Razor šablony mohou být předány jako argumenty bez vizuálního vzhledu součásti nebo vykresluje přímo.</span><span class="sxs-lookup"><span data-stu-id="17c29-478">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="17c29-479">Předchozí šablony jsou přímo vykreslí následujícím kódem Razor:</span><span class="sxs-lookup"><span data-stu-id="17c29-479">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="17c29-480">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="17c29-480">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="17c29-481">Ruční RenderTreeBuilder logiky</span><span class="sxs-lookup"><span data-stu-id="17c29-481">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="17c29-482">`Microsoft.AspNetCore.Components.RenderTree` poskytuje metody pro manipulaci s komponentami a prvky, včetně sestavení součástí ručně v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="17c29-482">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="17c29-483">Použití `RenderTreeBuilder` vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="17c29-483">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="17c29-484">Poškozená součásti (například značku neuzavřený značek) může způsobit nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="17c29-484">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="17c29-485">Vezměte v úvahu následující `PetDetails` komponenty, které ručně se dají do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="17c29-485">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@code
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="17c29-486">V následujícím příkladu, smyčky v `CreateComponent` metoda generuje tři `PetDetails` komponenty.</span><span class="sxs-lookup"><span data-stu-id="17c29-486">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="17c29-487">Při volání metody `RenderTreeBuilder` metody vytvoření součásti (`OpenComponent` a `AddAttribute`), pořadová čísla jsou čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="17c29-487">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="17c29-488">Algoritmus rozdíl Blazor spoléhá na pořadová čísla odpovídající odlišné řádky kódu, není odlišné volání volání.</span><span class="sxs-lookup"><span data-stu-id="17c29-488">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="17c29-489">Při vytváření komponent pomocí `RenderTreeBuilder` metody, pevně argumenty pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="17c29-489">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="17c29-490">**Použití výpočtu nebo čítače k vygenerování pořadové číslo může vést ke špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="17c29-490">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="17c29-491">Další informace najdete v tématu [pořadová čísla se týkají pořadí čísel a provádění není řádků kódu](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) oddílu.</span><span class="sxs-lookup"><span data-stu-id="17c29-491">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="17c29-492">`BuiltContent` Komponenty:</span><span class="sxs-lookup"><span data-stu-id="17c29-492">`BuiltContent` component:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="@RenderComponent">
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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="17c29-493">Pořadová čísla se týkají pořadí čísel a provádění není řádků kódu</span><span class="sxs-lookup"><span data-stu-id="17c29-493">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="17c29-494">Blazor `.razor` jsou vždycky soubory zkompilovány.</span><span class="sxs-lookup"><span data-stu-id="17c29-494">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="17c29-495">Toto je potenciálně skvělé výhody pro `.razor` protože kompilační krok je možné vložit informace, které zvýšit výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="17c29-495">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="17c29-496">Klíče příklad tato vylepšení zahrnují *pořadí čísla*.</span><span class="sxs-lookup"><span data-stu-id="17c29-496">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="17c29-497">Pořadová čísla oznámení modulu runtime, který výstupy pochází z různých a seřazené řádky kódu, které.</span><span class="sxs-lookup"><span data-stu-id="17c29-497">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="17c29-498">Modul runtime používá tyto informace ke generování efektivní stromu rozdíly v lineárním čase, což je mnohem rychleji, než je obvykle možné pro algoritmus diff obecné stromu.</span><span class="sxs-lookup"><span data-stu-id="17c29-498">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="17c29-499">Vezměte v úvahu následující jednoduchý `.razor` souboru:</span><span class="sxs-lookup"><span data-stu-id="17c29-499">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="17c29-500">Předchozí kód se zkompiluje pro přibližně takto:</span><span class="sxs-lookup"><span data-stu-id="17c29-500">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="17c29-501">Když kód spustí poprvé, pokud `someFlag` je `true`, obdrží Tvůrce:</span><span class="sxs-lookup"><span data-stu-id="17c29-501">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="17c29-502">Sequence</span><span class="sxs-lookup"><span data-stu-id="17c29-502">Sequence</span></span> | <span data-ttu-id="17c29-503">type</span><span class="sxs-lookup"><span data-stu-id="17c29-503">Type</span></span>      | <span data-ttu-id="17c29-504">Data</span><span class="sxs-lookup"><span data-stu-id="17c29-504">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="17c29-505">0</span><span class="sxs-lookup"><span data-stu-id="17c29-505">0</span></span>        | <span data-ttu-id="17c29-506">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="17c29-506">Text node</span></span> | <span data-ttu-id="17c29-507">první</span><span class="sxs-lookup"><span data-stu-id="17c29-507">First</span></span>  |
| <span data-ttu-id="17c29-508">1</span><span class="sxs-lookup"><span data-stu-id="17c29-508">1</span></span>        | <span data-ttu-id="17c29-509">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="17c29-509">Text node</span></span> | <span data-ttu-id="17c29-510">Sekunda</span><span class="sxs-lookup"><span data-stu-id="17c29-510">Second</span></span> |

<span data-ttu-id="17c29-511">Představte si, že `someFlag` stane `false`, a je znovu vykreslí značku.</span><span class="sxs-lookup"><span data-stu-id="17c29-511">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="17c29-512">Tentokrát, obdrží Tvůrce:</span><span class="sxs-lookup"><span data-stu-id="17c29-512">This time, the builder receives:</span></span>

| <span data-ttu-id="17c29-513">Sequence</span><span class="sxs-lookup"><span data-stu-id="17c29-513">Sequence</span></span> | <span data-ttu-id="17c29-514">type</span><span class="sxs-lookup"><span data-stu-id="17c29-514">Type</span></span>       | <span data-ttu-id="17c29-515">Data</span><span class="sxs-lookup"><span data-stu-id="17c29-515">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="17c29-516">1</span><span class="sxs-lookup"><span data-stu-id="17c29-516">1</span></span>        | <span data-ttu-id="17c29-517">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="17c29-517">Text node</span></span>  | <span data-ttu-id="17c29-518">Sekunda</span><span class="sxs-lookup"><span data-stu-id="17c29-518">Second</span></span> |

<span data-ttu-id="17c29-519">Když modul runtime provádí rozdílu, uvidí, která položka na pořadí `0` byla odebrána, takže generuje následující triviální *upravit skript*:</span><span class="sxs-lookup"><span data-stu-id="17c29-519">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="17c29-520">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="17c29-520">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="17c29-521">Co dojde k chybě, když vygenerujete pořadová čísla prostřednictvím kódu programu</span><span class="sxs-lookup"><span data-stu-id="17c29-521">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="17c29-522">Představte si, že jste naprogramovali proto, že následující vykreslení stromu Tvůrce logiky:</span><span class="sxs-lookup"><span data-stu-id="17c29-522">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="17c29-523">Nyní první výstup je následující:</span><span class="sxs-lookup"><span data-stu-id="17c29-523">Now, the first output is:</span></span>

<span data-ttu-id="17c29-524">| Pořadí | Typ      | Data   | | :------: | --------- | :--- : | | 0        | Textový uzel | První   | | 1        | Textový uzel | Druhý |</span><span class="sxs-lookup"><span data-stu-id="17c29-524">| Sequence | Type      | Data   | | :------: | --------- | :--- : | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="17c29-525">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="17c29-525">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="17c29-526">`someFlag` je `false` v druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="17c29-526">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="17c29-527">Sequence</span><span class="sxs-lookup"><span data-stu-id="17c29-527">Sequence</span></span> | <span data-ttu-id="17c29-528">type</span><span class="sxs-lookup"><span data-stu-id="17c29-528">Type</span></span>      | <span data-ttu-id="17c29-529">Data</span><span class="sxs-lookup"><span data-stu-id="17c29-529">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="17c29-530">0</span><span class="sxs-lookup"><span data-stu-id="17c29-530">0</span></span>        | <span data-ttu-id="17c29-531">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="17c29-531">Text node</span></span> | <span data-ttu-id="17c29-532">Sekunda</span><span class="sxs-lookup"><span data-stu-id="17c29-532">Second</span></span> |

<span data-ttu-id="17c29-533">Tentokrát vidí diff algoritmus, který *dvě* změny došlo, a algoritmus generuje následující skript upravit:</span><span class="sxs-lookup"><span data-stu-id="17c29-533">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="17c29-534">Změňte hodnotu na prvním uzlu text do `Second`.</span><span class="sxs-lookup"><span data-stu-id="17c29-534">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="17c29-535">Druhý textový uzel odeberte.</span><span class="sxs-lookup"><span data-stu-id="17c29-535">Remove the second text node.</span></span>

<span data-ttu-id="17c29-536">Generování pořadová čísla došlo ke ztrátě všech užitečné informace o tom, kde `if/else` větve a smyčky, se vyskytoval v původní kód.</span><span class="sxs-lookup"><span data-stu-id="17c29-536">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="17c29-537">Výsledkem je rozdíl **dvakrát tak dlouho,** stejně jako předtím.</span><span class="sxs-lookup"><span data-stu-id="17c29-537">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="17c29-538">Toto je příklad jednoduchého dotazu.</span><span class="sxs-lookup"><span data-stu-id="17c29-538">This is a trivial example.</span></span> <span data-ttu-id="17c29-539">Ve víc odpovídají realitě případů s komplexní a hluboce vnořené struktury a zejména s smyčky je závažnější snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="17c29-539">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="17c29-540">Místo okamžitě identifikaci, které bloky smyčky nebo větve bylo vloženo nebo odebrat, rozdíl algoritmus využívající dlaždice má recurse hluboko do stromové struktury vykreslování a obvykle mnohem delší dobu upravit skripty sestavení, protože je chybné o staré a nové struktury vzhledem k sobě navzájem.</span><span class="sxs-lookup"><span data-stu-id="17c29-540">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="17c29-541">Pokyny a z něj závěry</span><span class="sxs-lookup"><span data-stu-id="17c29-541">Guidance and conclusions</span></span>

* <span data-ttu-id="17c29-542">Výkon aplikace vyskytne-li dynamicky generované čísel.</span><span class="sxs-lookup"><span data-stu-id="17c29-542">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="17c29-543">Rozhraní framework nelze vytvořit své vlastní pořadová čísla automaticky za běhu protože potřebné informace neexistuje. Pokud je zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="17c29-543">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="17c29-544">Nezapisujte dlouhé bloky ručně implementované `RenderTreeBuilder` logiku.</span><span class="sxs-lookup"><span data-stu-id="17c29-544">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="17c29-545">Preferovat `.razor` soubory a umožňují kompilátoru řešit pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="17c29-545">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="17c29-546">Pokud pořadová čísla jsou pevně zakódované, algoritmus diff pouze vyžaduje, že pořadová čísla zvýšení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="17c29-546">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="17c29-547">Výchozí hodnoty a mezery nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="17c29-547">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="17c29-548">Jednou z legitimní možností je používat jako pořadové číslo, číslo řádku kódu nebo začátek od nuly a zvýšit případů nebo stovky (nebo libovolný upřednostňované interval).</span><span class="sxs-lookup"><span data-stu-id="17c29-548">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="17c29-549">Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní stromu rozdílování nepoužívejte.</span><span class="sxs-lookup"><span data-stu-id="17c29-549">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="17c29-550">Rozdílování je mnohem rychlejší, když se používají pořadová čísla a Blazor nabízí výhodu v podobě kompilační krok, který se zabývá pořadová čísla automaticky pro vývojáře pro tvorbu `.razor` soubory.</span><span class="sxs-lookup"><span data-stu-id="17c29-550">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
