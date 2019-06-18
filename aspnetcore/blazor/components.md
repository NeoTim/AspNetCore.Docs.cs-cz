---
title: Vytváření a používání komponent Razor
author: guardrex
description: Zjistěte, jak vytvořit a používat komponenty Razor, včetně jak vázat na data, zpracování událostí a spravovat životní cykly komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
uid: blazor/components
ms.openlocfilehash: 25743550c60e1d027066cdefe137148de74b0715
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196293"
---
# <a name="create-and-use-razor-components"></a><span data-ttu-id="1a563-103">Vytváření a používání komponent Razor</span><span class="sxs-lookup"><span data-stu-id="1a563-103">Create and use Razor components</span></span>

<span data-ttu-id="1a563-104">Podle [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a [Morné Zaayman](https://github.com/MorneZaayman)</span><span class="sxs-lookup"><span data-stu-id="1a563-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Morné Zaayman](https://github.com/MorneZaayman)</span></span>

<span data-ttu-id="1a563-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a563-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1a563-106">Blazor aplikace se vytvářejí pomocí *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="1a563-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="1a563-107">Komponenta je samostatná blok uživatelského rozhraní (UI), například stránky, dialogové okno nebo formuláře.</span><span class="sxs-lookup"><span data-stu-id="1a563-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="1a563-108">Komponenta obsahuje kód HTML a zpracování logiku potřebnou k vložení dat nebo v reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a563-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="1a563-109">Součásti jsou flexibilní a jednoduchý.</span><span class="sxs-lookup"><span data-stu-id="1a563-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="1a563-110">Mohou být vnořené, znovu použít a sdílet mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="1a563-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="1a563-111">Třídy součásti</span><span class="sxs-lookup"><span data-stu-id="1a563-111">Component classes</span></span>

<span data-ttu-id="1a563-112">Součásti jsou implementovány v [Razor](xref:mvc/views/razor) soubory součástí ( *.razor*) pomocí kombinace C# a značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="1a563-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span>

<span data-ttu-id="1a563-113">Dají se vytvářet komponenty pomocí *.cshtml* příponu souboru, tak dlouho, dokud soubory jsou označeny jako soubory součástí Razor pomocí `_RazorComponentInclude` vlastnost MSBuild.</span><span class="sxs-lookup"><span data-stu-id="1a563-113">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="1a563-114">Například aplikaci, která určuje, že všechny *.cshtml* soubory pod *stránky* složky mají být považována za soubory součástí Razor:</span><span class="sxs-lookup"><span data-stu-id="1a563-114">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="1a563-115">Uživatelské rozhraní pro součást je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="1a563-115">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="1a563-116">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="1a563-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="1a563-117">Při kompilaci aplikace, bude značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-117">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="1a563-118">Název generované třídy odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="1a563-118">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="1a563-119">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="1a563-119">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="1a563-120">V `@code` bloku, stav komponent (vlastnosti, pole) zadán s parametrem metody pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="1a563-120">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="1a563-121">Více než jeden `@code` blok je povolený.</span><span class="sxs-lookup"><span data-stu-id="1a563-121">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="1a563-122">V předchozích verzích technologie ASP.NET Core `@functions` bloky byly použity ke stejnému účelu jako `@code` bloky.</span><span class="sxs-lookup"><span data-stu-id="1a563-122">In previous versions of ASP.NET Core, `@functions` blocks were used for the same purpose as `@code` blocks.</span></span> <span data-ttu-id="1a563-123">`@functions` bloky i nadále fungovat, ale doporučujeme používat `@code` směrnice.</span><span class="sxs-lookup"><span data-stu-id="1a563-123">`@functions` blocks continue to work, but we recommend using the `@code` directive.</span></span>

<span data-ttu-id="1a563-124">Komponenta členy můžete poté použita jako tato součást je vykreslování pomocí logiky C# výrazy, které začínají `@`.</span><span class="sxs-lookup"><span data-stu-id="1a563-124">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="1a563-125">Například C# pole se vykreslí vložením prefixu `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="1a563-125">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="1a563-126">Následující příklad vyhodnotí a vykreslí:</span><span class="sxs-lookup"><span data-stu-id="1a563-126">The following example evaluates and renders:</span></span>

* <span data-ttu-id="1a563-127">`_headingFontStyle` Hodnota vlastnosti šablon stylů CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="1a563-127">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="1a563-128">`_headingText` k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="1a563-128">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="1a563-129">Po se zpočátku zobrazí komponentu obnoví komponenty jeho vykreslení stromu v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="1a563-129">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="1a563-130">Blazor poté porovnává větve vykreslení oproti předchozímu a použije všechny změny do prohlížeče Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="1a563-130">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="1a563-131">Součásti jsou běžné C# třídy a může být umístěna kdekoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="1a563-131">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="1a563-132">Součásti, které se obvykle vytvářejí webové stránky se nacházejí v *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="1a563-132">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="1a563-133">Součásti non-page se často umístí do *Shared* nebo vlastní složky přidán do projektu.</span><span class="sxs-lookup"><span data-stu-id="1a563-133">Non-page components are frequently placed into the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="1a563-134">Pokud chcete použít vlastní složku, buď přidejte vlastní složky oboru názvů na nadřazenou komponentu nebo na aplikaci  *\_Imports.razor* souboru.</span><span class="sxs-lookup"><span data-stu-id="1a563-134">To use a custom folder, either add the custom folder's namespace to the parent component or to the app's *\_Imports.razor* file.</span></span> <span data-ttu-id="1a563-135">Například následující obor názvů umožňuje součástí *součásti* složky, které jsou k dispozici, když je aplikace kořenový obor názvů `WebApplication`:</span><span class="sxs-lookup"><span data-stu-id="1a563-135">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="1a563-136">Integrovat komponenty do aplikace Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="1a563-136">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="1a563-137">Komponenty pomocí stávající aplikace Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="1a563-137">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="1a563-138">Není nutné pro přepsání existujících stránek nebo zobrazení Razor komponent.</span><span class="sxs-lookup"><span data-stu-id="1a563-138">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="1a563-139">Při zobrazení stránky nebo zobrazení se komponenty jsou předkreslených&dagger; ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="1a563-139">When the page or view is rendered, components are prerendered&dagger; at the same time.</span></span> 

> [!NOTE]
> <span data-ttu-id="1a563-140">&dagger;Dokončení fáze před vykreslením na straně serveru je zapnutá pro aplikace na straně serveru Blazor ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="1a563-140">&dagger;Server-side prerendering is enabled for Blazor server-side apps by default.</span></span> <span data-ttu-id="1a563-141">Aplikace na straně klienta Blazor bude podporovat dokončení fáze před vykreslením v nadcházející verzi Preview 5.</span><span class="sxs-lookup"><span data-stu-id="1a563-141">Client-side Blazor apps will support prerendering in the upcoming Preview 5 release.</span></span> <span data-ttu-id="1a563-142">Další informace najdete v tématu [aktualizace šablony/middlewaru, který má použít MapFallbackToPage/soubor](https://github.com/aspnet/AspNetCore/issues/8852).</span><span class="sxs-lookup"><span data-stu-id="1a563-142">For more information, see [Update templates/middleware to use MapFallbackToPage/File](https://github.com/aspnet/AspNetCore/issues/8852).</span></span>

<span data-ttu-id="1a563-143">K vykreslení komponenty z stránku nebo zobrazení, použijte `RenderComponentAsync<TComponent>` metodu helper HTML:</span><span class="sxs-lookup"><span data-stu-id="1a563-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="1a563-144">Při zobrazení stránky a můžou používat komponenty, neplatí první.</span><span class="sxs-lookup"><span data-stu-id="1a563-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="1a563-145">Součásti nelze použít zobrazení a stránky konkrétní scénáře, jako je částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="1a563-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="1a563-146">Chcete-li použít logiku z částečného zobrazení v komponentě, faktor si logiky částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="1a563-147">Další informace o způsobu vykreslené a komponenty stav součásti je spravováno v Blazor serverové aplikace, najdete v článku <xref:blazor/hosting-models> článku.</span><span class="sxs-lookup"><span data-stu-id="1a563-147">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="using-components"></a><span data-ttu-id="1a563-148">Pomocí komponent</span><span class="sxs-lookup"><span data-stu-id="1a563-148">Using components</span></span>

<span data-ttu-id="1a563-149">Součásti můžete zahrnout další součásti je deklarací pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="1a563-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="1a563-150">Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.</span><span class="sxs-lookup"><span data-stu-id="1a563-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="1a563-151">Následující kód v *Index.razor* vykreslí `HeadingComponent` instance:</span><span class="sxs-lookup"><span data-stu-id="1a563-151">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="1a563-152">*Components/HeadingComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="1a563-152">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

## <a name="component-parameters"></a><span data-ttu-id="1a563-153">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="1a563-153">Component parameters</span></span>

<span data-ttu-id="1a563-154">Může mít komponenty *parametry komponenty*, které jsou definovány pomocí *neveřejné* vlastnosti komponentní třída s `[Parameter]` atribut.</span><span class="sxs-lookup"><span data-stu-id="1a563-154">Components can have *component parameters*, which are defined using *non-public* properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="1a563-155">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="1a563-155">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="1a563-156">V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` vlastnost `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="1a563-156">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="1a563-157">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="1a563-157">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

<span data-ttu-id="1a563-158">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="1a563-158">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

## <a name="child-content"></a><span data-ttu-id="1a563-159">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="1a563-159">Child content</span></span>

<span data-ttu-id="1a563-160">Součásti můžete nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="1a563-160">Components can set the content of another component.</span></span> <span data-ttu-id="1a563-161">Přiřazení součásti najdete zde obsah mezi značky, které určují přijímající komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-161">The assigning component provides the content between the tags that specify the receiving component.</span></span> <span data-ttu-id="1a563-162">Například `ParentComponent` můžete zadat obsah pro vykreslování podle podřízené součásti tak, že je obsah uvnitř `<ChildComponent>` značky.</span><span class="sxs-lookup"><span data-stu-id="1a563-162">For example, a `ParentComponent` can provide content for rendering by a Child component by placing the content inside `<ChildComponent>` tags.</span></span>

<span data-ttu-id="1a563-163">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="1a563-163">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

<span data-ttu-id="1a563-164">Obsahuje podřízené součásti `ChildContent` vlastnost, která představuje `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="1a563-164">The Child component has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="1a563-165">Hodnota `ChildContent` je umístěn ve značkách podřízené součásti, kde má být vykreslen obsah.</span><span class="sxs-lookup"><span data-stu-id="1a563-165">The value of `ChildContent` is positioned in the child component's markup where the content should be rendered.</span></span> <span data-ttu-id="1a563-166">V následujícím příkladu, hodnota `ChildContent` přijme od nadřazené komponenty a vykreslit v rámci panelu Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="1a563-166">In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="1a563-167">*Components/ChildComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="1a563-167">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="1a563-168">Vlastnost příjmu `RenderFragment` obsahu musí mít název `ChildContent` konvencí.</span><span class="sxs-lookup"><span data-stu-id="1a563-168">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

## <a name="data-binding"></a><span data-ttu-id="1a563-169">Datová vazba</span><span class="sxs-lookup"><span data-stu-id="1a563-169">Data binding</span></span>

<span data-ttu-id="1a563-170">Vazba dat na komponent a prvky modelu DOM se dosahuje pomocí `@bind` atribut.</span><span class="sxs-lookup"><span data-stu-id="1a563-170">Data binding to both components and DOM elements is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="1a563-171">Následující příklad vytvoří vazbu `_italicsCheck` pole na zaškrtávací políčko zaškrtnuto, stav:</span><span class="sxs-lookup"><span data-stu-id="1a563-171">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="1a563-172">Při zaškrtnutí políčka je a zrušte zaškrtnutí, hodnota vlastnosti je aktualizována na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="1a563-172">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="1a563-173">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní, pouze v případě, že součást je vykresleno, ne v reakci na měnící se hodnota vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="1a563-173">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="1a563-174">Protože komponenty vykreslování sami po spuštění kódu obslužné rutiny události, aktualizace vlastností se obvykle projeví v uživatelském rozhraní ihned.</span><span class="sxs-lookup"><span data-stu-id="1a563-174">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="1a563-175">Pomocí `@bind` s `CurrentValue` vlastnosti (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="1a563-175">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="1a563-176">Při vykreslování komponentu `value` elementu input pochází z `CurrentValue` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1a563-176">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="1a563-177">Když uživatel zadá v textovém poli `onchange` událost se aktivuje a `CurrentValue` je nastavena na hodnotu změněné.</span><span class="sxs-lookup"><span data-stu-id="1a563-177">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="1a563-178">Ve skutečnosti je generování kódu poněkud složitější, protože `@bind` zpracovává několik případů, kdy jsou provedeny převody typu.</span><span class="sxs-lookup"><span data-stu-id="1a563-178">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="1a563-179">V zásadě `@bind` přidruží aktuální hodnotu výrazu s `value` obslužné rutiny a atributu změny pomocí zaregistrovaná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="1a563-179">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="1a563-180">Kromě `onchange`, vlastnost může být vázána pomocí jiné události, jako jsou `oninput` tak, že přidáte `@bind` atributem `event` parametr:</span><span class="sxs-lookup"><span data-stu-id="1a563-180">In addition to `onchange`, the property can be bound using other events like `oninput` by adding a `@bind` attribute with an `event` parameter:</span></span>

```cshtml
<input type="text" @bind-value="@CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="1a563-181">Na rozdíl od `onchange`, `oninput` aktivována pro každý znak, který je vstup do textového pole.</span><span class="sxs-lookup"><span data-stu-id="1a563-181">Unlike `onchange`, `oninput` fires for every character that is input into the text box.</span></span>

<span data-ttu-id="1a563-182">**Formátovací řetězce**</span><span class="sxs-lookup"><span data-stu-id="1a563-182">**Format strings**</span></span>

<span data-ttu-id="1a563-183">Vytváření datových vazeb funguje s <xref:System.DateTime> řetězce formátu.</span><span class="sxs-lookup"><span data-stu-id="1a563-183">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="1a563-184">V tuto chvíli nejsou k dispozici jiných výrazech formátu, například měny nebo číselných formátů.</span><span class="sxs-lookup"><span data-stu-id="1a563-184">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="1a563-185">`@bind:format` Atribut specifikuje formát data použít `value` z `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="1a563-185">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="1a563-186">Formát slouží také k analýze hodnotu při `onchange` dojde k události.</span><span class="sxs-lookup"><span data-stu-id="1a563-186">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="1a563-187">**Parametry komponenty**</span><span class="sxs-lookup"><span data-stu-id="1a563-187">**Component parameters**</span></span>

<span data-ttu-id="1a563-188">Vazba také rozpozná parametry komponenty, kde `@bind-{property}` mohl vytvořit vazbu vlastnosti komponentami.</span><span class="sxs-lookup"><span data-stu-id="1a563-188">Binding also recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="1a563-189">Používá následující komponenty `ChildComponent` a vytvoří vazbu `ParentYear` parametr z nadřazeného `Year` parametru u podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="1a563-189">The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

<span data-ttu-id="1a563-190">Nadřazené komponenty:</span><span class="sxs-lookup"><span data-stu-id="1a563-190">Parent component:</span></span>

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

<span data-ttu-id="1a563-191">Podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="1a563-191">Child component:</span></span>

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

<span data-ttu-id="1a563-192">`EventCallback<T>` je podrobně [EventCallback](#eventcallback) oddílu.</span><span class="sxs-lookup"><span data-stu-id="1a563-192">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="1a563-193">Načítají `ParentComponent` vytváří následující značky:</span><span class="sxs-lookup"><span data-stu-id="1a563-193">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="1a563-194">Pokud hodnota `ParentYear` vlastnost se změní tak, že vyberete tlačítko v `ParentComponent`, `Year` vlastnost `ChildComponent` se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="1a563-194">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="1a563-195">Nová hodnota `Year` se vykreslí v uživatelském rozhraní při `ParentComponent` je rerendered:</span><span class="sxs-lookup"><span data-stu-id="1a563-195">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="1a563-196">`Year` Parametr je s možností vazby, protože má doprovodná `YearChanged` událost, která odpovídá typu používaného `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="1a563-196">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="1a563-197">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` je v podstatě ekvivalentní zápisu</span><span class="sxs-lookup"><span data-stu-id="1a563-197">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing,</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="1a563-198">Obecně platí, vlastnost může být vázána na odpovídající obslužná rutina události pomocí `@bind-property:event` atribut.</span><span class="sxs-lookup"><span data-stu-id="1a563-198">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="1a563-199">Například vlastnost `MyProp` může být vázaný na `MyEventHandler` pomocí následující dva atributy:</span><span class="sxs-lookup"><span data-stu-id="1a563-199">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<FooComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="1a563-200">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="1a563-200">Event handling</span></span>

<span data-ttu-id="1a563-201">Součásti syntaxe Razor poskytují funkce zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="1a563-201">Razor components provide event handling features.</span></span> <span data-ttu-id="1a563-202">Atribut HTML elementu s názvem `on<event>` (například `onclick`, `onsubmit`) s hodnotou typu delegáta Razor součásti považuje za hodnotu atributu obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="1a563-202">For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="1a563-203">Název atributu vždy začíná `@on`.</span><span class="sxs-lookup"><span data-stu-id="1a563-203">The attribute's name always starts with `@on`.</span></span>

<span data-ttu-id="1a563-204">Následující kód volá `UpdateHeading` metodu po výběru tlačítka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1a563-204">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="1a563-205">Následující kód volá `CheckboxChanged` metoda při změně zaškrtávacího políčka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1a563-205">The following code calls the `CheckboxChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="@CheckboxChanged" />

@code {
    private void CheckboxChanged()
    {
        ...
    }
}
```

<span data-ttu-id="1a563-206">Obslužné rutiny událostí může být také asynchronní a zpět <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="1a563-206">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="1a563-207">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="1a563-207">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="1a563-208">Výjimky se protokolují, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="1a563-208">Exceptions are logged when they occur.</span></span>

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

<span data-ttu-id="1a563-209">Pro některé události nejsou povoleny typy argumentů události specifické pro události.</span><span class="sxs-lookup"><span data-stu-id="1a563-209">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="1a563-210">Pokud přístup k jednomu z těchto typů událostí není nezbytné, není potřeba ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="1a563-210">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="1a563-211">Seznam podporovaných událostí argumentů je:</span><span class="sxs-lookup"><span data-stu-id="1a563-211">The list of supported event arguments is:</span></span>

* <span data-ttu-id="1a563-212">UIEventArgs</span><span class="sxs-lookup"><span data-stu-id="1a563-212">UIEventArgs</span></span>
* <span data-ttu-id="1a563-213">UIChangeEventArgs</span><span class="sxs-lookup"><span data-stu-id="1a563-213">UIChangeEventArgs</span></span>
* <span data-ttu-id="1a563-214">UIKeyboardEventArgs</span><span class="sxs-lookup"><span data-stu-id="1a563-214">UIKeyboardEventArgs</span></span>
* <span data-ttu-id="1a563-215">UIMouseEventArgs</span><span class="sxs-lookup"><span data-stu-id="1a563-215">UIMouseEventArgs</span></span>

<span data-ttu-id="1a563-216">Výrazy lambda lze také použít:</span><span class="sxs-lookup"><span data-stu-id="1a563-216">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="1a563-217">Často je vhodné zavřít další hodnoty, například během iterace přes sadu elementů.</span><span class="sxs-lookup"><span data-stu-id="1a563-217">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="1a563-218">Následující příklad vytvoří tři tlačítka, každý z který volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a tlačítko (`buttonNumber`) při výběru v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1a563-218">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="1a563-219">Proveďte **není** použít proměnnou smyčky (`i`) v `for` smyčky přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="1a563-219">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="1a563-220">V opačném případě se používá stejnou proměnnou všechny výrazy lambda způsobí `i`hodnotu být stejné ve všech výrazů lambda.</span><span class="sxs-lookup"><span data-stu-id="1a563-220">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="1a563-221">Zachytit její hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak přes ni.</span><span class="sxs-lookup"><span data-stu-id="1a563-221">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="1a563-222">EventCallback</span><span class="sxs-lookup"><span data-stu-id="1a563-222">EventCallback</span></span>

<span data-ttu-id="1a563-223">Je běžným scénářem vnořených součástí chce spouštět nadřazenou metodu komponenty, při výskytu události podřízené součásti&mdash;například, když `onclick` v podřízeném dojde k události.</span><span class="sxs-lookup"><span data-stu-id="1a563-223">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="1a563-224">Chcete-li zpřístupnit událostí mezi komponentami, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="1a563-224">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="1a563-225">Nadřazené komponenty můžete přiřadit metody zpětného volání k podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="1a563-225">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="1a563-226">Podřízené součásti v ukázkové aplikaci ukazuje, jak tlačítka `onclick` přijímat je nastavena obslužná rutina `EventCallback` delegovat z tohoto příkladu nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-226">The Child component in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's Parent component.</span></span> <span data-ttu-id="1a563-227">`EventCallback` Je zadán s `UIMouseEventArgs`, což je vhodné pro `onclick` událost z periferní zařízení:</span><span class="sxs-lookup"><span data-stu-id="1a563-227">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="1a563-228">Nadřazené komponenty nastaví dítěte `EventCallback<T>` k jeho `ShowMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="1a563-228">The Parent component sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="1a563-229">Při výběru tlačítka v podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="1a563-229">When the button is selected in the Child component:</span></span>

* <span data-ttu-id="1a563-230">Nadřazené komponenty `ShowMessage` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="1a563-230">The Parent component's `ShowMessage` method is called.</span></span> <span data-ttu-id="1a563-231">`messageText` se aktualizují a zobrazí v nadřazené součásti.</span><span class="sxs-lookup"><span data-stu-id="1a563-231">`messageText` is updated and displayed in the Parent component.</span></span>
* <span data-ttu-id="1a563-232">Volání `StateHasChanged` není nutné v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="1a563-232">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="1a563-233">`StateHasChanged` je volána automaticky k rerender nadřazené komponenty, stejně jako podřízené události aktivovat součást rerendering v obslužných rutinách událostí, které jsou spuštěny v rámci podřízené.</span><span class="sxs-lookup"><span data-stu-id="1a563-233">`StateHasChanged` is called automatically to rerender the Parent component, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="1a563-234">`EventCallback` a `EventCallback<T>` povolit asynchronních delegátů.</span><span class="sxs-lookup"><span data-stu-id="1a563-234">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="1a563-235">`EventCallback<T>` silně typované a vyžaduje konkrétní argument typu.</span><span class="sxs-lookup"><span data-stu-id="1a563-235">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="1a563-236">`EventCallback` slabě typované a jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-236">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="1a563-237">Vyvolání `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a operátoru await <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="1a563-237">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="1a563-238">Použití `EventCallback` a `EventCallback<T>` pro událost zpracování a vazby parametrů komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-238">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="1a563-239">Preferovat silného typu `EventCallback<T>` přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="1a563-239">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="1a563-240">`EventCallback<T>` poskytuje lepší zpětnou vazbu chyba uživatelům komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-240">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="1a563-241">Podobně jako jiné obslužné rutině události uživatelského rozhraní, zadání parametru události je volitelné.</span><span class="sxs-lookup"><span data-stu-id="1a563-241">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="1a563-242">Použití `EventCallback` při neexistuje převáděná hodnota předaný zpětnému volání.</span><span class="sxs-lookup"><span data-stu-id="1a563-242">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="1a563-243">Zachycení odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="1a563-243">Capture references to components</span></span>

<span data-ttu-id="1a563-244">Komponenta odkazy poskytují způsob, jak odkazovat na instanci komponenty tak, že můžete použít příkazy do této instance, jako například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="1a563-244">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="1a563-245">Chcete-li zachytit odkazem komponenty, přidejte `@ref` atribut podřízené součásti a pak definovat pole se stejným názvem a stejného typu jako podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="1a563-245">To capture a component reference, add a `@ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

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

<span data-ttu-id="1a563-246">Při vykreslování komponentu `loginDialog` pole se vyplní `MyLoginDialog` podřízené instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-246">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="1a563-247">Potom můžete vyvolat metody rozhraní .NET na instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-247">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1a563-248">`loginDialog` Proměnná je vyplněný pouze poté, co se vykreslí komponentu a zahrnuje její výstup `MyLoginDialog` elementu.</span><span class="sxs-lookup"><span data-stu-id="1a563-248">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="1a563-249">Až do bodu není nutné nic odkazovat.</span><span class="sxs-lookup"><span data-stu-id="1a563-249">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="1a563-250">K manipulaci s odkazy na součásti po dokončení vykreslení komponentu, použijte `OnAfterRenderAsync` nebo `OnAfterRender` metody.</span><span class="sxs-lookup"><span data-stu-id="1a563-250">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="1a563-251">Při zachytávání odkazů na komponenty pomocí syntaxe podobné [zachytávání odkazy na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) funkce.</span><span class="sxs-lookup"><span data-stu-id="1a563-251">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="1a563-252">Nejsou součástí odkazy předané do kódu jazyka JavaScript&mdash;se použít jenom v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="1a563-252">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="1a563-253">Proveďte **není** mutovat stavu podřízenými komponentami pomocí odkazů na komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-253">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="1a563-254">Místo toho použijte normální deklarované parametry k předání dat podřízenými komponentami.</span><span class="sxs-lookup"><span data-stu-id="1a563-254">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="1a563-255">To způsobí, že podřízené součásti rerender ve správném čase automaticky.</span><span class="sxs-lookup"><span data-stu-id="1a563-255">This causes child components to rerender at the correct times automatically.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="1a563-256">Použití @key řídit zachování prvky a součásti</span><span class="sxs-lookup"><span data-stu-id="1a563-256">Use @key to control the preservation of elements and components</span></span>

<span data-ttu-id="1a563-257">Při vykreslování seznamu elementů nebo komponenty prvky a součásti následně změnit, algoritmus rozdílování Blazor společnosti musíte rozhodnout, které z předchozí prvky nebo komponent se můžou ukládat a mapování objektů modelu do nich.</span><span class="sxs-lookup"><span data-stu-id="1a563-257">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="1a563-258">Za normálních okolností tento proces probíhá automaticky a můžete ignorovat, ale existují případy, kde můžete chtít řízení procesu.</span><span class="sxs-lookup"><span data-stu-id="1a563-258">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="1a563-259">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="1a563-259">Consider the following example:</span></span>

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

<span data-ttu-id="1a563-260">Obsah `People` kolekce může změnit s vložit, odstranit nebo měnit pořadí položek.</span><span class="sxs-lookup"><span data-stu-id="1a563-260">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="1a563-261">Když rerenders komponentu `<DetailsEditor>` komponenta může změnit na jiný `Details` hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="1a563-261">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="1a563-262">To může způsobit složitější rerendering, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="1a563-262">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="1a563-263">V některých případech rerendering může vést k rozdíly v chování viditelné, například ke ztrátě prvek fokus.</span><span class="sxs-lookup"><span data-stu-id="1a563-263">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="1a563-264">Proces mapování se dá řídit pomocí `@key` atribut direktivy.</span><span class="sxs-lookup"><span data-stu-id="1a563-264">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="1a563-265">`@key` způsobí, že algoritmus rozdílování zaručit zachování elementy nebo komponenty založené na hodnotě klíče:</span><span class="sxs-lookup"><span data-stu-id="1a563-265">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="1a563-266">Když `People` změny kolekce algoritmus rozdílování uchovává přidružení mezi `<DetailsEditor>` instancí a `person` instancí:</span><span class="sxs-lookup"><span data-stu-id="1a563-266">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="1a563-267">Pokud `Person` je odstraněn z `People` seznamu pouze odpovídající `<DetailsEditor>` instance je odebrána z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a563-267">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="1a563-268">Jindy jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="1a563-268">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1a563-269">Pokud `Person` se vloží některé pozici v seznamu, jeden nové `<DetailsEditor>` instance bude vložena na této pozici odpovídající.</span><span class="sxs-lookup"><span data-stu-id="1a563-269">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="1a563-270">Jindy jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="1a563-270">Other instances are left unchanged.</span></span>
* <span data-ttu-id="1a563-271">Pokud `Person` jsou položky měnit pořadí, odpovídající `<DetailsEditor>` instancí jsou zachovány a pořadí v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a563-271">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="1a563-272">V některých scénářích použití `@key` minimalizuje rerendering složitost a potenciální problémy s stavové části modelu DOM změna například pozici fokus se vyhnete.</span><span class="sxs-lookup"><span data-stu-id="1a563-272">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1a563-273">Klíče jsou místní pro každý kontejner prvku nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-273">Keys are local to each container element or component.</span></span> <span data-ttu-id="1a563-274">Klíče jsou *není* porovnat se globálně dokumentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-274">Keys are *not* compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="1a563-275">Kdy použít @key</span><span class="sxs-lookup"><span data-stu-id="1a563-275">When to use @key</span></span>

<span data-ttu-id="1a563-276">Obvykle je vhodné použít `@key` vždy, když je vykreslen seznamu (například v `@foreach` bloku) a k definování existuje vhodnou hodnotu `@key`.</span><span class="sxs-lookup"><span data-stu-id="1a563-276">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="1a563-277">Můžete také použít `@key` zabránit Blazor zachování prvku nebo komponenty podstrom, když se změní objekt:</span><span class="sxs-lookup"><span data-stu-id="1a563-277">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="1a563-278">Pokud `@currentPerson` změny, `@key` atribut direktivy vynutí Blazor zahodíte celý `<div>` a její potomci a znovu sestavte podstrom uživatelského rozhraní pomocí nové prvky a součásti.</span><span class="sxs-lookup"><span data-stu-id="1a563-278">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="1a563-279">To může být užitečné, pokud potřebujete zajistit, že žádný stav uživatelského rozhraní se zachová při `@currentPerson` změny.</span><span class="sxs-lookup"><span data-stu-id="1a563-279">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="1a563-280">Kdy nepoužívat @key</span><span class="sxs-lookup"><span data-stu-id="1a563-280">When not to use @key</span></span>

<span data-ttu-id="1a563-281">Výkon, když je rozdílování s `@key`.</span><span class="sxs-lookup"><span data-stu-id="1a563-281">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="1a563-282">Není velké náklady na výkon, ale pouze zadat `@key` Pokud řízení zachování prvku nebo komponenty pravidla využívat aplikace.</span><span class="sxs-lookup"><span data-stu-id="1a563-282">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="1a563-283">I když `@key` není použit, Blazor zachová podřízený element a součást instance co největší míře.</span><span class="sxs-lookup"><span data-stu-id="1a563-283">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="1a563-284">Jedinou výhodou používání `@key` je ovládací prvek průběhu *jak* instancí modelu jsou mapovány na instance zachovaných součástí, namísto rozdílování algoritmus výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="1a563-284">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="1a563-285">Jaké hodnoty je třeba použít pro @key</span><span class="sxs-lookup"><span data-stu-id="1a563-285">What values to use for @key</span></span>

<span data-ttu-id="1a563-286">Obecně je vhodné jej zadejte hodnoty pro následující typy `@key`:</span><span class="sxs-lookup"><span data-stu-id="1a563-286">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="1a563-287">Model instance objektů (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="1a563-287">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="1a563-288">Tím se zajistí zachování založené na objektu referenční rovnost.</span><span class="sxs-lookup"><span data-stu-id="1a563-288">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="1a563-289">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`, nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="1a563-289">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="1a563-290">Vyhněte se zadání hodnoty, které mohou kolidovat neočekávaně.</span><span class="sxs-lookup"><span data-stu-id="1a563-290">Avoid supplying a value that can clash unexpectedly.</span></span> <span data-ttu-id="1a563-291">Pokud `@key="@someObject.GetHashCode()"` pochází, může dojít k neočekávaným došlo ke konfliktům protože kódů hash objektů nesouvisejících může být stejný.</span><span class="sxs-lookup"><span data-stu-id="1a563-291">If `@key="@someObject.GetHashCode()"` is supplied, unexpected clashes may occur because the hash codes of unrelated objects can be the same.</span></span> <span data-ttu-id="1a563-292">Pokud se kolidující `@key` hodnoty jsou požadovány v rámci stejné nadřazené `@key` nebude respektovat hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1a563-292">If clashing `@key` values are requested within the same parent, the `@key` values won't be honored.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="1a563-293">Životní cyklus metody</span><span class="sxs-lookup"><span data-stu-id="1a563-293">Lifecycle methods</span></span>

<span data-ttu-id="1a563-294">`OnInitAsync` a `OnInit` spouštění kódu se inicializovat komponentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-294">`OnInitAsync` and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="1a563-295">Chcete-li provádění asynchronní operace, použijte `OnInitAsync` a `await` – klíčové slovo v operaci:</span><span class="sxs-lookup"><span data-stu-id="1a563-295">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="1a563-296">Synchronní operace, použijte `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="1a563-296">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="1a563-297">`OnParametersSetAsync` a `OnParametersSet` se volá, když součást přijme parametry ze svého nadřazeného objektu a hodnoty jsou přiřazeny k vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="1a563-297">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="1a563-298">Tyto metody jsou provedeny po inicializaci součásti a pokaždé, když je vykreslen komponenty:</span><span class="sxs-lookup"><span data-stu-id="1a563-298">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="1a563-299">`OnAfterRenderAsync` a `OnAfterRender` se volá se po vykreslení komponentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-299">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="1a563-300">V tomto okamžiku se vyplní elementu a součást odkazy.</span><span class="sxs-lookup"><span data-stu-id="1a563-300">Element and component references are populated at this point.</span></span> <span data-ttu-id="1a563-301">Pomocí této fázi můžete provést další inicializaci postup pomocí vykreslovaný obsah, jako je aktivace JavaScript knihovny třetích stran, které pracují s vykreslené elementy modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="1a563-301">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

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

<span data-ttu-id="1a563-302">`SetParameters` může být potlačena za účelem spouštění kódu předtím, než jsou nastavené parametry:</span><span class="sxs-lookup"><span data-stu-id="1a563-302">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="1a563-303">Pokud `base.SetParameters` není vyvolána, můžete vlastní kód interpretaci příchozí hodnoty parametrů v jakékoli požadované stejně, jako.</span><span class="sxs-lookup"><span data-stu-id="1a563-303">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="1a563-304">Například příchozí parametry nejsou potřeba přiřadit k vlastnosti ve třídě.</span><span class="sxs-lookup"><span data-stu-id="1a563-304">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

<span data-ttu-id="1a563-305">`ShouldRender` může být potlačena za účelem potlačit aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a563-305">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="1a563-306">Implementace vrátí-li `true`, aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a563-306">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="1a563-307">I když `ShouldRender` je přepsána, komponenta je vždy Počáteční vykreslení.</span><span class="sxs-lookup"><span data-stu-id="1a563-307">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="1a563-308">Vyřazení komponenty pomocí rozhraní IDisposable</span><span class="sxs-lookup"><span data-stu-id="1a563-308">Component disposal with IDisposable</span></span>

<span data-ttu-id="1a563-309">Pokud komponenta implementuje <xref:System.IDisposable>, [metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) se volá, když je součást z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a563-309">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="1a563-310">Používá následující komponenty `@implements IDisposable` a `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="1a563-310">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="1a563-311">Směrování</span><span class="sxs-lookup"><span data-stu-id="1a563-311">Routing</span></span>

<span data-ttu-id="1a563-312">Směrování v Blazor se dosahuje tím, že poskytuje šablona trasy pro jednotlivé dostupné komponenty v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1a563-312">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="1a563-313">Když soubor Razor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="1a563-313">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="1a563-314">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="1a563-314">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="1a563-315">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-315">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="1a563-316">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="1a563-316">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="1a563-317">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="1a563-317">Route parameters</span></span>

<span data-ttu-id="1a563-318">Součásti mohou přijímat parametry trasy z šablonu trasy, které jsou součástí `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="1a563-318">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="1a563-319">Směrovač používá parametry trasy k naplnění odpovídající komponenta parametry.</span><span class="sxs-lookup"><span data-stu-id="1a563-319">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="1a563-320">*Komponenta parametr trasa*:</span><span class="sxs-lookup"><span data-stu-id="1a563-320">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="1a563-321">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="1a563-321">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="1a563-322">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="1a563-322">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="1a563-323">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1a563-323">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="1a563-324">Základní třída dědičnosti "použití modelu code-behind" prostředí</span><span class="sxs-lookup"><span data-stu-id="1a563-324">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="1a563-325">Soubory součástí kombinovat kód HTML a C# zpracování kódu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="1a563-325">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="1a563-326">`@inherits` – Direktiva je možné poskytnout Blazor aplikace, který odděluje komponenty značek z kódu pro zpracování prostředí "použití modelu code-behind".</span><span class="sxs-lookup"><span data-stu-id="1a563-326">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="1a563-327">[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak komponenty může dědit základní třídy `BlazorRocksBase`, aby vznikl komponenty vlastnosti a metody.</span><span class="sxs-lookup"><span data-stu-id="1a563-327">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="1a563-328">*Komponenta Blazor Rocks*:</span><span class="sxs-lookup"><span data-stu-id="1a563-328">*Blazor Rocks component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="1a563-329">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a563-329">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="1a563-330">Základní třída musí být odvozený z: `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="1a563-330">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="1a563-331">Importovat komponenty</span><span class="sxs-lookup"><span data-stu-id="1a563-331">Import components</span></span>

<span data-ttu-id="1a563-332">Obor názvů součásti zleva doprava Razor podle:</span><span class="sxs-lookup"><span data-stu-id="1a563-332">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="1a563-333">V projektu `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="1a563-333">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="1a563-334">Cesta z kořenového adresáře projektu do komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-334">The path from the project root to the component.</span></span> <span data-ttu-id="1a563-335">Například `ComponentsSample/Pages/Index.razor` je v oboru názvů `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="1a563-335">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="1a563-336">Postupujte podle součásti C# název pravidel vazby.</span><span class="sxs-lookup"><span data-stu-id="1a563-336">Components follow C# name binding rules.</span></span> <span data-ttu-id="1a563-337">V případě třídy *Index.razor*, všechny součásti ve stejné složce *stránky*a nadřazené složky *ComponentsSample*, jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="1a563-337">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="1a563-338">Součásti definované v jiný obor názvů může být přenesena do rozsahu pomocí syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice.</span><span class="sxs-lookup"><span data-stu-id="1a563-338">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="1a563-339">Pokud jiná komponenta, `NavMenu.razor`, existuje ve složce `ComponentsSample/Shared/`, součást je možné v `Index.razor` následujícím `@using` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="1a563-339">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="1a563-340">Součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, která eliminuje potřebu [ \@pomocí](xref:mvc/views/razor#using) – direktiva:</span><span class="sxs-lookup"><span data-stu-id="1a563-340">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="1a563-341">`global::` Kvalifikace se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1a563-341">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="1a563-342">Import součásti s aliasem `using` příkazy (například `@using Foo = Bar`) se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1a563-342">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="1a563-343">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="1a563-343">Partially qualified names aren't supported.</span></span> <span data-ttu-id="1a563-344">Například přidáním `@using ComponentsSample` a odkazování na ně `NavMenu.razor` s `<Shared.NavMenu></Shared.NavMenu>` se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1a563-344">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="razor-support"></a><span data-ttu-id="1a563-345">Podpora Razor</span><span class="sxs-lookup"><span data-stu-id="1a563-345">Razor support</span></span>

<span data-ttu-id="1a563-346">**Direktivy Razor**</span><span class="sxs-lookup"><span data-stu-id="1a563-346">**Razor directives**</span></span>

<span data-ttu-id="1a563-347">V následující tabulce jsou uvedeny direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="1a563-347">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="1a563-348">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="1a563-348">Directive</span></span> | <span data-ttu-id="1a563-349">Popis</span><span class="sxs-lookup"><span data-stu-id="1a563-349">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="1a563-350">\@code</span><span class="sxs-lookup"><span data-stu-id="1a563-350">\@code</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="1a563-351">Přidá C# blok kódu na komponentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-351">Adds a C# code block to a component.</span></span> <span data-ttu-id="1a563-352">`@code` je alias pro `@functions`.</span><span class="sxs-lookup"><span data-stu-id="1a563-352">`@code` is an alias of `@functions`.</span></span> <span data-ttu-id="1a563-353">`@code` doporučuje se přes `@functions`.</span><span class="sxs-lookup"><span data-stu-id="1a563-353">`@code` is recommended over `@functions`.</span></span> <span data-ttu-id="1a563-354">Více než jeden `@code` blok je povolený.</span><span class="sxs-lookup"><span data-stu-id="1a563-354">More than one `@code` block is permissible.</span></span> |
| [<span data-ttu-id="1a563-355">\@Funkce</span><span class="sxs-lookup"><span data-stu-id="1a563-355">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="1a563-356">Přidá C# blok kódu na komponentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-356">Adds a C# code block to a component.</span></span> <span data-ttu-id="1a563-357">Zvolte `@code` přes `@functions` pro C# bloky kódu.</span><span class="sxs-lookup"><span data-stu-id="1a563-357">Choose `@code` over `@functions` for C# code blocks.</span></span> |
| `@implements` | <span data-ttu-id="1a563-358">Implementuje rozhraní pro třídu vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-358">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="1a563-359">\@inherits</span><span class="sxs-lookup"><span data-stu-id="1a563-359">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="1a563-360">Poskytuje plnou kontrolu nad třídu, která dědí komponentu.</span><span class="sxs-lookup"><span data-stu-id="1a563-360">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="1a563-361">\@Vložení</span><span class="sxs-lookup"><span data-stu-id="1a563-361">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="1a563-362">Vkládání ze služby umožňuje [kontejneru služby](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1a563-362">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1a563-363">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1a563-363">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="1a563-364">Určuje komponentu rozložení.</span><span class="sxs-lookup"><span data-stu-id="1a563-364">Specifies a layout component.</span></span> <span data-ttu-id="1a563-365">Rozložení komponenty umožňují zabránit zdvojení kódu a nekonzistence.</span><span class="sxs-lookup"><span data-stu-id="1a563-365">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="1a563-366">\@Stránka</span><span class="sxs-lookup"><span data-stu-id="1a563-366">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="1a563-367">Určuje, že by měla komponenta zpracování požadavků přímo.</span><span class="sxs-lookup"><span data-stu-id="1a563-367">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="1a563-368">`@page` – Direktiva je možné zadat při trasy a volitelné parametry.</span><span class="sxs-lookup"><span data-stu-id="1a563-368">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="1a563-369">Na rozdíl od Razor Pages `@page` – direktiva nemusí být první – direktiva v horní části souboru.</span><span class="sxs-lookup"><span data-stu-id="1a563-369">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="1a563-370">Další informace najdete v tématu [směrování](xref:blazor/routing).</span><span class="sxs-lookup"><span data-stu-id="1a563-370">For more information, see [Routing](xref:blazor/routing).</span></span> |
| [<span data-ttu-id="1a563-371">\@použití</span><span class="sxs-lookup"><span data-stu-id="1a563-371">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="1a563-372">Přidá C# `using` směrnice do třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-372">Adds the C# `using` directive to the generated component class.</span></span> <span data-ttu-id="1a563-373">To přináší také všechny součásti, které jsou definované v tomto oboru názvů do oboru.</span><span class="sxs-lookup"><span data-stu-id="1a563-373">This also brings all the components defined in that namespace into scope.</span></span> |
| [<span data-ttu-id="1a563-374">\@namespace</span><span class="sxs-lookup"><span data-stu-id="1a563-374">\@namespace</span></span>](xref:mvc/views/razor#section-6) | <span data-ttu-id="1a563-375">Nastaví obor názvů, třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-375">Sets the namespace of the generated component class.</span></span> |
| [<span data-ttu-id="1a563-376">\@attribute</span><span class="sxs-lookup"><span data-stu-id="1a563-376">\@attribute</span></span>](xref:mvc/views/razor#section-7) | <span data-ttu-id="1a563-377">Atribut se přidá do třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-377">Adds an attribute to the generated component class.</span></span> |

<span data-ttu-id="1a563-378">**Podmíněné atributy prvků HTML**</span><span class="sxs-lookup"><span data-stu-id="1a563-378">**Conditional HTML element attributes**</span></span>

<span data-ttu-id="1a563-379">Atributy prvků HTML jsou vykreslovány podmíněně na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="1a563-379">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="1a563-380">Pokud je hodnota `false` nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="1a563-380">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="1a563-381">Pokud je hodnota `true`, atribut je vykreslen minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="1a563-381">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="1a563-382">V následujícím příkladu `IsCompleted` Určuje, zda `checked` se vykreslí v elementu značky:</span><span class="sxs-lookup"><span data-stu-id="1a563-382">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="1a563-383">Pokud `IsCompleted` je `true`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="1a563-383">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="1a563-384">Pokud `IsCompleted` je `false`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="1a563-384">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="1a563-385">**Další informace o syntaxi Razor**</span><span class="sxs-lookup"><span data-stu-id="1a563-385">**Additional information on Razor**</span></span>

<span data-ttu-id="1a563-386">Další informace o syntaxi Razor, najdete v článku [referenční příručka syntaxe Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="1a563-386">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="1a563-387">Raw HTML</span><span class="sxs-lookup"><span data-stu-id="1a563-387">Raw HTML</span></span>

<span data-ttu-id="1a563-388">Řetězce jsou obvykle vykreslen pomocí modelu DOM textové uzly, což znamená, že všechny značky, které mohou obsahovat je ignorována a považována jako prostý text.</span><span class="sxs-lookup"><span data-stu-id="1a563-388">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="1a563-389">Pokud chcete zobrazit nezpracovaný kód HTML, zabalit obsah HTML v `MarkupString` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1a563-389">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="1a563-390">Hodnota je analyzovat ve formátu HTML nebo SVG a vložit do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="1a563-390">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="1a563-391">Vykreslování nezpracovaný kód HTML vytvořený z libovolného nedůvěryhodný zdroj je **bezpečnostní riziko** a mělo by se vyhnout!</span><span class="sxs-lookup"><span data-stu-id="1a563-391">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="1a563-392">Následující příklad ukazuje použití `MarkupString` typu přidáte blok statického obsahu HTML vykresleného výstupu součásti:</span><span class="sxs-lookup"><span data-stu-id="1a563-392">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="1a563-393">Bez vizuálního vzhledu součásti</span><span class="sxs-lookup"><span data-stu-id="1a563-393">Templated components</span></span>

<span data-ttu-id="1a563-394">Bez vizuálního vzhledu komponenty jsou součástí, které přijímají jeden nebo více šablon uživatelského rozhraní jako parametry, které lze použít jako součást logiky komponenty vykreslování.</span><span class="sxs-lookup"><span data-stu-id="1a563-394">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="1a563-395">Bez vizuálního vzhledu komponenty umožňují vytvářet vyšší úrovně součásti, které jsou více než regulární komponenty opakovaně použitelné.</span><span class="sxs-lookup"><span data-stu-id="1a563-395">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="1a563-396">Zahrnují několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="1a563-396">A couple of examples include:</span></span>

* <span data-ttu-id="1a563-397">Komponenta tabulky, která umožňuje uživateli zadat šablony pro záhlaví tabulky, řádky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="1a563-397">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="1a563-398">Seznam součástí, která umožňuje uživateli zadat šablonu pro vykreslování položky v seznamu.</span><span class="sxs-lookup"><span data-stu-id="1a563-398">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="1a563-399">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="1a563-399">Template parameters</span></span>

<span data-ttu-id="1a563-400">Bez vizuálního vzhledu součásti je definován tak, že zadáte jeden nebo více parametrů součást typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="1a563-400">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="1a563-401">Vykreslení fragment reprezentuje segment, který uživatelského rozhraní, které je vykresleno komponentou.</span><span class="sxs-lookup"><span data-stu-id="1a563-401">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="1a563-402">Vykreslení fragment volitelně přebírá parametr, který lze zadat, pokud je vyvolána fragment vykreslení.</span><span class="sxs-lookup"><span data-stu-id="1a563-402">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="1a563-403">*Tabulka součásti šablony*:</span><span class="sxs-lookup"><span data-stu-id="1a563-403">*Table Template component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="1a563-404">Při použití komponenty bez vizuálního vzhledu, parametry šablony lze pomocí podřízené prvky, které odpovídají názvům parametry (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="1a563-404">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="1a563-405">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="1a563-405">Template context parameters</span></span>

<span data-ttu-id="1a563-406">Komponenta argumenty typu `RenderFragment<T>` předaný jako elementy mají implicitní parametr s názvem `context` (například z předchozí příklad kódu `@context.PetId`), ale můžete změnit pomocí parametru název `Context` atribut na podřízené element.</span><span class="sxs-lookup"><span data-stu-id="1a563-406">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="1a563-407">V následujícím příkladu `RowTemplate` elementu `Context` Určuje atribut `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="1a563-407">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="1a563-408">Alternativně můžete zadat `Context` atribut na prvek součásti.</span><span class="sxs-lookup"><span data-stu-id="1a563-408">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="1a563-409">Zadaný `Context` atributu platí pro všechny parametry určené šablony.</span><span class="sxs-lookup"><span data-stu-id="1a563-409">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="1a563-410">To může být užitečné, pokud chcete zadat název obsahu parametru pro implicitní podřízený obsah (bez jakékoli zabalení podřízený element).</span><span class="sxs-lookup"><span data-stu-id="1a563-410">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="1a563-411">V následujícím příkladu `Context` atributu se zobrazí na `TableTemplate` elementu a platí pro všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="1a563-411">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="1a563-412">Obecné typy komponenty</span><span class="sxs-lookup"><span data-stu-id="1a563-412">Generic-typed components</span></span>

<span data-ttu-id="1a563-413">Bez vizuálního vzhledu součásti jsou často obecně typu.</span><span class="sxs-lookup"><span data-stu-id="1a563-413">Templated components are often generically typed.</span></span> <span data-ttu-id="1a563-414">Například komponentu obecného seznamu zobrazit šablonu můžete použít k vykreslení `IEnumerable<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1a563-414">For example, a generic List View Template component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="1a563-415">K definování obecné součásti, použijte `@typeparam` směrnice a určete parametry typu.</span><span class="sxs-lookup"><span data-stu-id="1a563-415">To define a generic component, use the `@typeparam` directive to specify type parameters.</span></span>

<span data-ttu-id="1a563-416">*Součásti šablony ListView*:</span><span class="sxs-lookup"><span data-stu-id="1a563-416">*ListView Template component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="1a563-417">Při použití komponenty obecného typu, parametr typu je odvozený Pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="1a563-417">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="1a563-418">V opačném případě parametr typu musí být explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="1a563-418">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="1a563-419">V následujícím příkladu `TItem="Pet"` Určuje typ:</span><span class="sxs-lookup"><span data-stu-id="1a563-419">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="1a563-420">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="1a563-420">Cascading values and parameters</span></span>

<span data-ttu-id="1a563-421">V některých případech je nevhodné toku dat z jako součást nadřazené komponenty potomka pomocí [parametry komponenty](#component-parameters), zvlášť pokud máte několik vrstev komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-421">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="1a563-422">Kaskádové hodnoty a parametry tento problém vyřešit tím, že poskytuje pohodlný způsob pro komponentu předchůdce k zadání hodnoty pro všechny jeho podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="1a563-422">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="1a563-423">Kaskádové hodnoty a parametry také poskytnout přístup pro součásti pro koordinaci.</span><span class="sxs-lookup"><span data-stu-id="1a563-423">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="1a563-424">Příklad motiv</span><span class="sxs-lookup"><span data-stu-id="1a563-424">Theme example</span></span>

<span data-ttu-id="1a563-425">V následujícím *motiv* příklad z ukázkové aplikace `ThemeInfo` třída určuje informace o motivech tok součástí hierarchií směrem dolů, aby všechna tlačítka v rámci dané části aplikace sdílet stejný styl.</span><span class="sxs-lookup"><span data-stu-id="1a563-425">In the following *Theme* example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="1a563-426">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a563-426">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="1a563-427">Jako součást předchůdce může poskytnout kaskádové hodnotu pomocí komponenty kaskádové hodnotu.</span><span class="sxs-lookup"><span data-stu-id="1a563-427">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="1a563-428">Součást CSS hodnoty zabalí podstrom součástí hierarchie a poskytuje jednu hodnotu pro všechny komponenty v rámci této podstrom.</span><span class="sxs-lookup"><span data-stu-id="1a563-428">The Cascading Value component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="1a563-429">Například ukázkové aplikace určuje informace o motivech (`ThemeInfo`) v jednom z rozložení aplikace jako parametr šablony pro všechny součásti, které tvoří rozložení textu `@Body` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1a563-429">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="1a563-430">`ButtonClass` je přiřazena hodnota `btn-success` v komponentě rozložení.</span><span class="sxs-lookup"><span data-stu-id="1a563-430">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="1a563-431">Všechny podřízené součásti mohou využívat tuto vlastnost prostřednictvím `ThemeInfo` šablony objektu.</span><span class="sxs-lookup"><span data-stu-id="1a563-431">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="1a563-432">*Kaskádové součást hodnoty parametrů rozložení*:</span><span class="sxs-lookup"><span data-stu-id="1a563-432">*Cascading Values Parameters Layout component*:</span></span>

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

<span data-ttu-id="1a563-433">Chcete-li pomocí kaskádových hodnot, komponenty deklarovat kaskádové parametry s využitím `[CascadingParameter]` atribut nebo na základě hodnoty názvu řetězec:</span><span class="sxs-lookup"><span data-stu-id="1a563-433">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="1a563-434">Vazba s hodnotou řetězce názvu platí, pokud máte více kaskádových hodnot stejného typu a pro jejich odlišení v rámci stejné podstrom.</span><span class="sxs-lookup"><span data-stu-id="1a563-434">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="1a563-435">Kaskádové hodnoty se váží k parametrům šablony podle typu.</span><span class="sxs-lookup"><span data-stu-id="1a563-435">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="1a563-436">V ukázkové aplikaci, komponentě CSS motiv hodnoty parametrů váže `ThemeInfo` kaskádové hodnotu pro parametr šablony.</span><span class="sxs-lookup"><span data-stu-id="1a563-436">In the sample app, the Cascading Values Parameters Theme component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="1a563-437">Tento parametr se používá nastavení třídy šablony stylů CSS pro jedno z tlačítek zobrazí komponentou.</span><span class="sxs-lookup"><span data-stu-id="1a563-437">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="1a563-438">*Kaskádové součást hodnoty parametrů motiv*:</span><span class="sxs-lookup"><span data-stu-id="1a563-438">*Cascading Values Parameters Theme component*:</span></span>

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

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a><span data-ttu-id="1a563-439">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="1a563-439">TabSet example</span></span>

<span data-ttu-id="1a563-440">Parametry šablony také povolit součásti spolupracovat napříč hierarchií komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-440">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="1a563-441">Představte si třeba následující *TabSet* příklad v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1a563-441">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="1a563-442">Ukázková aplikace má `ITab` rozhraní, které karty implementace:</span><span class="sxs-lookup"><span data-stu-id="1a563-442">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="1a563-443">Komponenta CSS hodnoty parametrů TabSet používá součásti záložku, která obsahuje několik komponent kartu:</span><span class="sxs-lookup"><span data-stu-id="1a563-443">The Cascading Values Parameters TabSet component uses the Tab Set component, which contains several Tab components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="1a563-444">Podřízené kartu komponenty nejsou explicitně předány jako parametry na kartě nastavení.</span><span class="sxs-lookup"><span data-stu-id="1a563-444">The child Tab components aren't explicitly passed as parameters to the Tab Set.</span></span> <span data-ttu-id="1a563-445">Místo toho podřízené kartu komponenty jsou součástí podřízenému obsahu ovládacího prvku na kartě nastavení.</span><span class="sxs-lookup"><span data-stu-id="1a563-445">Instead, the child Tab components are part of the child content of the Tab Set.</span></span> <span data-ttu-id="1a563-446">Však nastavení kartu stále potřebuje vědět o jednotlivých součástech kartu tak, aby ho může mít za následek záhlaví a na aktivní kartě. Povolit koordinace bez potřeby dalšího kódu, nastavte kartu komponenty *samotný můžete zadat jako hodnotu kaskádové* , který se potom vybere potomka kartu komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a563-446">However, the Tab Set still needs to know about each Tab component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the Tab Set component *can provide itself as a cascading value* that is then picked up by the descendent Tab components.</span></span>

<span data-ttu-id="1a563-447">*Komponenta TabSet*:</span><span class="sxs-lookup"><span data-stu-id="1a563-447">*TabSet component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="1a563-448">Podřízené součásti zachycení kartu obsahující kartu nastavit jako parametr šablony, karta součásti přidaly na kartu Nastavení a souřadnice na které kartě je aktivní.</span><span class="sxs-lookup"><span data-stu-id="1a563-448">The descendent Tab components capture the containing Tab Set as a cascading parameter, so the Tab components add themselves to the Tab Set and coordinate on which tab is active.</span></span>

<span data-ttu-id="1a563-449">*Karta komponenty*:</span><span class="sxs-lookup"><span data-stu-id="1a563-449">*Tab component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="1a563-450">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="1a563-450">Razor templates</span></span>

<span data-ttu-id="1a563-451">Vykreslení fragmenty lze definovat pomocí syntaxe šablon Razor.</span><span class="sxs-lookup"><span data-stu-id="1a563-451">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="1a563-452">Šablony Razor představují způsob, jak definovat fragment kódu uživatelského rozhraní a předpokládají následující formát:</span><span class="sxs-lookup"><span data-stu-id="1a563-452">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="1a563-453">Následující příklad ukazuje, jak určit `RenderFragment` a `RenderFragment<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1a563-453">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="1a563-454">*Součásti šablony Razor*:</span><span class="sxs-lookup"><span data-stu-id="1a563-454">*Razor Templates component*:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="1a563-455">Vykreslení fragmenty definované pomocí syntaxe Razor šablony mohou být předány jako argumenty bez vizuálního vzhledu součásti nebo vykresluje přímo.</span><span class="sxs-lookup"><span data-stu-id="1a563-455">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="1a563-456">Předchozí šablony jsou přímo vykreslí následujícím kódem Razor:</span><span class="sxs-lookup"><span data-stu-id="1a563-456">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="1a563-457">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="1a563-457">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="1a563-458">Ruční RenderTreeBuilder logiky</span><span class="sxs-lookup"><span data-stu-id="1a563-458">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="1a563-459">`Microsoft.AspNetCore.Components.RenderTree` poskytuje metody pro manipulaci s komponentami a prvky, včetně sestavení součástí ručně v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="1a563-459">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="1a563-460">Použití `RenderTreeBuilder` vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="1a563-460">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="1a563-461">Poškozená součásti (například značku neuzavřený značek) může způsobit nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="1a563-461">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="1a563-462">Vezměte v úvahu následující podrobnosti domácí mazlíček součásti, která ručně se dají do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="1a563-462">Consider the following Pet Details component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@code
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="1a563-463">V následujícím příkladu, smyčky v `CreateComponent` metoda generuje tři komponenty domácí mazlíček podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="1a563-463">In the following example, the loop in the `CreateComponent` method generates three Pet Details components.</span></span> <span data-ttu-id="1a563-464">Při volání metody `RenderTreeBuilder` metody vytvoření součásti (`OpenComponent` a `AddAttribute`), pořadová čísla jsou čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="1a563-464">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="1a563-465">Algoritmus rozdíl Blazor spoléhá na pořadová čísla odpovídající odlišné řádky kódu, není odlišné volání volání.</span><span class="sxs-lookup"><span data-stu-id="1a563-465">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="1a563-466">Při vytváření komponent pomocí `RenderTreeBuilder` metody, pevně argumenty pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="1a563-466">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="1a563-467">**Použití výpočtu nebo čítače k vygenerování pořadové číslo může vést ke špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="1a563-467">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="1a563-468">Další informace najdete v tématu [pořadová čísla se týkají pořadí čísel a provádění není řádků kódu](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) oddílu.</span><span class="sxs-lookup"><span data-stu-id="1a563-468">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="1a563-469">*Integrované komponenty obsah*:</span><span class="sxs-lookup"><span data-stu-id="1a563-469">*Built Content component*:</span></span>

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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="1a563-470">Pořadová čísla se týkají pořadí čísel a provádění není řádků kódu</span><span class="sxs-lookup"><span data-stu-id="1a563-470">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="1a563-471">Blazor `.razor` jsou vždycky soubory zkompilovány.</span><span class="sxs-lookup"><span data-stu-id="1a563-471">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="1a563-472">Toto je potenciálně skvělé výhody pro `.razor` protože kompilační krok je možné vložit informace, které zvýšit výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="1a563-472">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="1a563-473">Klíče příklad tato vylepšení zahrnují *pořadí čísla*.</span><span class="sxs-lookup"><span data-stu-id="1a563-473">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="1a563-474">Pořadová čísla oznámení modulu runtime, který výstupy pochází z různých a seřazené řádky kódu, které.</span><span class="sxs-lookup"><span data-stu-id="1a563-474">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="1a563-475">Modul runtime používá tyto informace ke generování efektivní stromu rozdíly v lineárním čase, což je mnohem rychleji, než je obvykle možné pro algoritmus diff obecné stromu.</span><span class="sxs-lookup"><span data-stu-id="1a563-475">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="1a563-476">Vezměte v úvahu následující jednoduchý `.razor` souboru:</span><span class="sxs-lookup"><span data-stu-id="1a563-476">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="1a563-477">To se kompiluje na přibližně takto:</span><span class="sxs-lookup"><span data-stu-id="1a563-477">This compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="1a563-478">Když tento kód spustí poprvé, pokud `someFlag` je `true`, obdrží Tvůrce:</span><span class="sxs-lookup"><span data-stu-id="1a563-478">When this code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="1a563-479">Sequence</span><span class="sxs-lookup"><span data-stu-id="1a563-479">Sequence</span></span> | <span data-ttu-id="1a563-480">Type</span><span class="sxs-lookup"><span data-stu-id="1a563-480">Type</span></span>      | <span data-ttu-id="1a563-481">Data</span><span class="sxs-lookup"><span data-stu-id="1a563-481">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="1a563-482">0</span><span class="sxs-lookup"><span data-stu-id="1a563-482">0</span></span>        | <span data-ttu-id="1a563-483">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="1a563-483">Text node</span></span> | <span data-ttu-id="1a563-484">první</span><span class="sxs-lookup"><span data-stu-id="1a563-484">First</span></span>  |
| <span data-ttu-id="1a563-485">1</span><span class="sxs-lookup"><span data-stu-id="1a563-485">1</span></span>        | <span data-ttu-id="1a563-486">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="1a563-486">Text node</span></span> | <span data-ttu-id="1a563-487">Sekunda</span><span class="sxs-lookup"><span data-stu-id="1a563-487">Second</span></span> |

<span data-ttu-id="1a563-488">Nyní Představte si, že `someFlag` stane `false`, a My se pak znovu.</span><span class="sxs-lookup"><span data-stu-id="1a563-488">Now imagine that `someFlag` becomes `false`, and we render again.</span></span> <span data-ttu-id="1a563-489">Tentokrát, obdrží Tvůrce:</span><span class="sxs-lookup"><span data-stu-id="1a563-489">This time, the builder receives:</span></span>

| <span data-ttu-id="1a563-490">Sequence</span><span class="sxs-lookup"><span data-stu-id="1a563-490">Sequence</span></span> | <span data-ttu-id="1a563-491">Type</span><span class="sxs-lookup"><span data-stu-id="1a563-491">Type</span></span>       | <span data-ttu-id="1a563-492">Data</span><span class="sxs-lookup"><span data-stu-id="1a563-492">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="1a563-493">1</span><span class="sxs-lookup"><span data-stu-id="1a563-493">1</span></span>        | <span data-ttu-id="1a563-494">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="1a563-494">Text node</span></span>  | <span data-ttu-id="1a563-495">Sekunda</span><span class="sxs-lookup"><span data-stu-id="1a563-495">Second</span></span> |

<span data-ttu-id="1a563-496">Když modul runtime provádí rozdílu, uvidí, která položka na pořadí `0` byla odebrána, takže generuje následující triviální *upravit skript*:</span><span class="sxs-lookup"><span data-stu-id="1a563-496">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="1a563-497">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="1a563-497">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="1a563-498">Co dojde k chybě, když vygenerujete pořadová čísla prostřednictvím kódu programu</span><span class="sxs-lookup"><span data-stu-id="1a563-498">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="1a563-499">Představte si, že jste napsali následující logice rendertree Tvůrce:</span><span class="sxs-lookup"><span data-stu-id="1a563-499">Imagine instead that you wrote the following rendertree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="1a563-500">Teď by byl první výstup:</span><span class="sxs-lookup"><span data-stu-id="1a563-500">Now the first output would be:</span></span>

<span data-ttu-id="1a563-501">| Pořadí | Typ | Data || :------: | --------- | :--- : | | 0 | Textový uzel | První || 1 | Textový uzel | Druhý |</span><span class="sxs-lookup"><span data-stu-id="1a563-501">| Sequence | Type      | Data   | | :------: | --------- | :--- : | | 0        | Text node | First  | | 1        | Text node | Second |</span></span>

<span data-ttu-id="1a563-502">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="1a563-502">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="1a563-503">V druhém vykreslování, když `someFlag` je `false`, zobrazí se výstup:</span><span class="sxs-lookup"><span data-stu-id="1a563-503">On the second render when `someFlag` is `false`, the output is:</span></span>

| <span data-ttu-id="1a563-504">Sequence</span><span class="sxs-lookup"><span data-stu-id="1a563-504">Sequence</span></span> | <span data-ttu-id="1a563-505">Type</span><span class="sxs-lookup"><span data-stu-id="1a563-505">Type</span></span>      | <span data-ttu-id="1a563-506">Data</span><span class="sxs-lookup"><span data-stu-id="1a563-506">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="1a563-507">0</span><span class="sxs-lookup"><span data-stu-id="1a563-507">0</span></span>        | <span data-ttu-id="1a563-508">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="1a563-508">Text node</span></span> | <span data-ttu-id="1a563-509">Sekunda</span><span class="sxs-lookup"><span data-stu-id="1a563-509">Second</span></span> |

<span data-ttu-id="1a563-510">Tentokrát vidí diff algoritmus, který *dvě* změny došlo, a algoritmus generuje následující skript upravit:</span><span class="sxs-lookup"><span data-stu-id="1a563-510">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="1a563-511">Změňte hodnotu na prvním uzlu text do `Second`.</span><span class="sxs-lookup"><span data-stu-id="1a563-511">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="1a563-512">Druhý textový uzel odeberte.</span><span class="sxs-lookup"><span data-stu-id="1a563-512">Remove the second text node.</span></span>

<span data-ttu-id="1a563-513">Generování pořadová čísla došlo ke ztrátě všech užitečné informace o tom, kde `if/else` větve a smyčky, se vyskytoval v původní kód.</span><span class="sxs-lookup"><span data-stu-id="1a563-513">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="1a563-514">Výsledkem je rozdíl **dvakrát tak dlouho,** stejně jako předtím.</span><span class="sxs-lookup"><span data-stu-id="1a563-514">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="1a563-515">Toto je příklad jednoduchého dotazu.</span><span class="sxs-lookup"><span data-stu-id="1a563-515">This is a trivial example.</span></span> <span data-ttu-id="1a563-516">Ve víc odpovídají realitě případů s komplexní a hluboce vnořené struktury a zejména s smyčky je závažnější snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="1a563-516">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="1a563-517">Místo okamžitě identifikaci, které bloky smyčky nebo větve bylo vloženo nebo odebrat, rozdíl algoritmus využívající dlaždice má recurse hluboko do stromové struktury vykreslování a obvykle mnohem delší dobu upravit skripty sestavení, protože je chybné o staré a nové struktury vzhledem k sobě navzájem.</span><span class="sxs-lookup"><span data-stu-id="1a563-517">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="1a563-518">Pokyny a z něj závěry</span><span class="sxs-lookup"><span data-stu-id="1a563-518">Guidance and conclusions</span></span>

* <span data-ttu-id="1a563-519">Výkon aplikace vyskytne-li dynamicky generované čísel.</span><span class="sxs-lookup"><span data-stu-id="1a563-519">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="1a563-520">Rozhraní framework nelze vytvořit své vlastní pořadová čísla automaticky za běhu protože potřebné informace neexistuje. Pokud je zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="1a563-520">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="1a563-521">Nezapisujte dlouhé bloky ručně implementované `RenderTreeBuilder` logiku.</span><span class="sxs-lookup"><span data-stu-id="1a563-521">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="1a563-522">Preferovat `.razor` soubory a umožňují kompilátoru řešit pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="1a563-522">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="1a563-523">Pokud pořadová čísla jsou pevně zakódované, algoritmus diff pouze vyžaduje, že pořadová čísla zvýšení hodnoty.</span><span class="sxs-lookup"><span data-stu-id="1a563-523">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="1a563-524">Výchozí hodnoty a mezery nejsou relevantní.</span><span class="sxs-lookup"><span data-stu-id="1a563-524">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="1a563-525">Jednou z legitimní možností je používat jako pořadové číslo, číslo řádku kódu nebo začátek od nuly a zvýšit případů nebo stovky (nebo libovolný upřednostňované interval).</span><span class="sxs-lookup"><span data-stu-id="1a563-525">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="1a563-526">Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní stromu rozdílování nepoužívejte.</span><span class="sxs-lookup"><span data-stu-id="1a563-526">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="1a563-527">Rozdílování je mnohem rychlejší, když se používají pořadová čísla a Blazor nabízí výhodu v podobě kompilační krok, který se zabývá pořadová čísla automaticky pro vývojáře pro tvorbu `.razor` soubory.</span><span class="sxs-lookup"><span data-stu-id="1a563-527">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
