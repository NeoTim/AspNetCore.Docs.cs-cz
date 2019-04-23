---
title: Vytváření a používání komponent Razor
author: guardrex
description: Zjistěte, jak vytvořit a používat komponenty Razor, včetně jak vázat na data, zpracování událostí a spravovat životní cykly komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2019
uid: blazor/components
ms.openlocfilehash: e318d262063c83fc17068ef65018174e2a4c953c
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59983033"
---
# <a name="create-and-use-razor-components"></a><span data-ttu-id="f22e1-103">Vytváření a používání komponent Razor</span><span class="sxs-lookup"><span data-stu-id="f22e1-103">Create and use Razor components</span></span>

<span data-ttu-id="f22e1-104">Podle [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a [Morné Zaayman](https://github.com/MorneZaayman)</span><span class="sxs-lookup"><span data-stu-id="f22e1-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Morné Zaayman](https://github.com/MorneZaayman)</span></span>

<span data-ttu-id="f22e1-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f22e1-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f22e1-106">Blazor aplikace se vytvářejí pomocí *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="f22e1-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="f22e1-107">Komponenta je samostatná blok uživatelského rozhraní (UI), například stránky, dialogové okno nebo formuláře.</span><span class="sxs-lookup"><span data-stu-id="f22e1-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="f22e1-108">Komponenta obsahuje kód HTML a zpracování logiku potřebnou k vložení dat nebo v reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f22e1-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="f22e1-109">Součásti jsou flexibilní a jednoduchý.</span><span class="sxs-lookup"><span data-stu-id="f22e1-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="f22e1-110">Mohou být vnořené, znovu použít a sdílet mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="f22e1-111">Třídy součásti</span><span class="sxs-lookup"><span data-stu-id="f22e1-111">Component classes</span></span>

<span data-ttu-id="f22e1-112">Součásti jsou implementovány v souborech Razor součásti (*.razor*) pomocí kombinace C# a značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="f22e1-112">Components are implemented in Razor component files (*.razor*) using a combination of C# and HTML markup.</span></span>

<span data-ttu-id="f22e1-113">Dají se vytvářet komponenty pomocí *.cshtml* příponu souboru, tak dlouho, dokud soubory jsou označeny jako soubory součástí Razor pomocí `_RazorComponentInclude` vlastnost MSBuild.</span><span class="sxs-lookup"><span data-stu-id="f22e1-113">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="f22e1-114">Například aplikace vytvořené pomocí komponenty šablona Razor Určuje, že všechny *.cshtml* soubory pod *stránky* složky mají být považována za soubory součástí Razor:</span><span class="sxs-lookup"><span data-stu-id="f22e1-114">For example, an app created using the Razor component template specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="f22e1-115">Uživatelské rozhraní pro součást je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="f22e1-115">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="f22e1-116">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="f22e1-116">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="f22e1-117">Při kompilaci aplikace, bude značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-117">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="f22e1-118">Název generované třídy odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="f22e1-118">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="f22e1-119">Členy třídy komponenty jsou definovány v `@functions` blok (více než jeden `@functions` blok je povolený).</span><span class="sxs-lookup"><span data-stu-id="f22e1-119">Members of the component class are defined in an `@functions` block (more than one `@functions` block is permissible).</span></span> <span data-ttu-id="f22e1-120">V `@functions` bloku, stav komponent (vlastnosti, pole) zadán s parametrem metody pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="f22e1-120">In the `@functions` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span>

<span data-ttu-id="f22e1-121">Komponenta členy můžete poté použita jako tato součást je vykreslování pomocí logiky C# výrazy, které začínají `@`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-121">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="f22e1-122">Například C# pole se vykreslí vložením prefixu `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="f22e1-122">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="f22e1-123">Následující příklad vyhodnotí a vykreslí:</span><span class="sxs-lookup"><span data-stu-id="f22e1-123">The following example evaluates and renders:</span></span>

* <span data-ttu-id="f22e1-124">`_headingFontStyle` Hodnota vlastnosti šablon stylů CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-124">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="f22e1-125">`_headingText` k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-125">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="f22e1-126">Po se zpočátku zobrazí komponentu obnoví komponenty jeho vykreslení stromu v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="f22e1-126">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="f22e1-127">Blazor poté porovnává větve vykreslení oproti předchozímu a použije všechny změny do prohlížeče Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="f22e1-127">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="f22e1-128">Integrovat komponenty do aplikace Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="f22e1-128">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="f22e1-129">Komponenty pomocí stávající aplikace Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="f22e1-129">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="f22e1-130">Není nutné pro přepsání existujících stránek nebo zobrazení Razor komponent.</span><span class="sxs-lookup"><span data-stu-id="f22e1-130">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="f22e1-131">Při zobrazení stránky nebo zobrazení se komponenty jsou předkreslených&dagger; ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-131">When the page or view is rendered, components are prerendered&dagger; at the same time.</span></span> 

> [!NOTE]
> <span data-ttu-id="f22e1-132">&dagger;Dokončení fáze před vykreslením na straně serveru je zapnutá pro aplikace na straně serveru Blazor ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="f22e1-132">&dagger;Server-side prerendering is enabled for Blazor server-side apps by default.</span></span> <span data-ttu-id="f22e1-133">Aplikace na straně klienta Blazor bude podporovat dokončení fáze před vykreslením v nadcházející verzi Preview 5.</span><span class="sxs-lookup"><span data-stu-id="f22e1-133">Client-side Blazor apps will support prerendering in the upcoming Preview 5 release.</span></span> <span data-ttu-id="f22e1-134">Další informace najdete v tématu [aktualizace šablony/middlewaru, který má použít MapFallbackToPage/soubor](https://github.com/aspnet/AspNetCore/issues/8852).</span><span class="sxs-lookup"><span data-stu-id="f22e1-134">For more information, see [Update templates/middleware to use MapFallbackToPage/File](https://github.com/aspnet/AspNetCore/issues/8852).</span></span>

<span data-ttu-id="f22e1-135">K vykreslení komponenty z stránku nebo zobrazení, použijte `RenderComponentAsync<TComponent>` metodu helper HTML:</span><span class="sxs-lookup"><span data-stu-id="f22e1-135">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="f22e1-136">Při zobrazení stránky a můžou používat komponenty, neplatí první.</span><span class="sxs-lookup"><span data-stu-id="f22e1-136">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="f22e1-137">Součásti nelze použít zobrazení a stránky konkrétní scénáře, jako je částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="f22e1-137">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="f22e1-138">Chcete-li použít logiku z částečného zobrazení v komponentě, faktor si logiky částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-138">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="f22e1-139">Další informace o způsobu vykreslené a komponenty stav součásti je spravováno v Blazor serverové aplikace, najdete v článku <xref:blazor/hosting-models> článku.</span><span class="sxs-lookup"><span data-stu-id="f22e1-139">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="using-components"></a><span data-ttu-id="f22e1-140">Pomocí komponent</span><span class="sxs-lookup"><span data-stu-id="f22e1-140">Using components</span></span>

<span data-ttu-id="f22e1-141">Součásti můžete zahrnout další součásti je deklarací pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="f22e1-141">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="f22e1-142">Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-142">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="f22e1-143">Následující kód vykreslí `HeadingComponent` instance:</span><span class="sxs-lookup"><span data-stu-id="f22e1-143">The following markup renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a><span data-ttu-id="f22e1-144">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="f22e1-144">Component parameters</span></span>

<span data-ttu-id="f22e1-145">Může mít komponenty *parametry komponenty*, které jsou definovány pomocí *neveřejné* vlastnosti komponentní třída upravené pomocí `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-145">Components can have *component parameters*, which are defined using *non-public* properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="f22e1-146">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-146">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="f22e1-147">V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` vlastnost `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="f22e1-147">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`:</span></span>

<span data-ttu-id="f22e1-148">*Nadřazené komponenty*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-148">*Parent component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

<span data-ttu-id="f22e1-149">*Podřízené součásti*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-149">*Child component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.razor?highlight=11-12)]

## <a name="child-content"></a><span data-ttu-id="f22e1-150">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="f22e1-150">Child content</span></span>

<span data-ttu-id="f22e1-151">Součásti můžete nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-151">Components can set the content of another component.</span></span> <span data-ttu-id="f22e1-152">Přiřazení součásti najdete zde obsah mezi značky, které určují přijímající komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-152">The assigning component provides the content between the tags that specify the receiving component.</span></span> <span data-ttu-id="f22e1-153">Například `ParentComponent` můžete zadat obsah pro vykreslování podle podřízené součásti tak, že je obsah uvnitř `<ChildComponent>` značky.</span><span class="sxs-lookup"><span data-stu-id="f22e1-153">For example, a `ParentComponent` can provide content for rendering by a Child component by placing the content inside `<ChildComponent>` tags.</span></span>

<span data-ttu-id="f22e1-154">*Nadřazené komponenty*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-154">*Parent component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

<span data-ttu-id="f22e1-155">Obsahuje podřízené součásti `ChildContent` vlastnost, která představuje `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-155">The Child component has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="f22e1-156">Hodnota `ChildContent` je umístěn ve značkách podřízené součásti, kde má být vykreslen obsah.</span><span class="sxs-lookup"><span data-stu-id="f22e1-156">The value of `ChildContent` is positioned in the child component's markup where the content should be rendered.</span></span> <span data-ttu-id="f22e1-157">V následujícím příkladu, hodnota `ChildContent` přijme od nadřazené komponenty a vykreslit v rámci panelu Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-157">In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="f22e1-158">*Podřízené součásti*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-158">*Child component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="f22e1-159">Vlastnost příjmu `RenderFragment` obsahu musí mít název `ChildContent` konvencí.</span><span class="sxs-lookup"><span data-stu-id="f22e1-159">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

## <a name="data-binding"></a><span data-ttu-id="f22e1-160">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="f22e1-160">Data binding</span></span>

<span data-ttu-id="f22e1-161">Vazba dat na komponent a prvky modelu DOM se dosahuje pomocí `bind` atribut.</span><span class="sxs-lookup"><span data-stu-id="f22e1-161">Data binding to both components and DOM elements is accomplished with the `bind` attribute.</span></span> <span data-ttu-id="f22e1-162">Následující příklad vytvoří vazbu `ItalicsCheck` vlastnost na zaškrtávací políčko zaškrtnuto, stav:</span><span class="sxs-lookup"><span data-stu-id="f22e1-162">The following example binds the `ItalicsCheck` property to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck">
```

<span data-ttu-id="f22e1-163">Při zaškrtnutí políčka je a zrušte zaškrtnutí, hodnota vlastnosti je aktualizována na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="f22e1-163">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="f22e1-164">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní, pouze v případě, že součást je vykresleno, ne v reakci na měnící se hodnota vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-164">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="f22e1-165">Protože komponenty vykreslování sami po spuštění kódu obslužné rutiny události, aktualizace vlastností se obvykle projeví v uživatelském rozhraní ihned.</span><span class="sxs-lookup"><span data-stu-id="f22e1-165">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="f22e1-166">Pomocí `bind` s `CurrentValue` vlastnosti (`<input bind="@CurrentValue">`) je v podstatě ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="f22e1-166">Using `bind` with a `CurrentValue` property (`<input bind="@CurrentValue">`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)">
```

<span data-ttu-id="f22e1-167">Při vykreslování komponentu `value` elementu input pochází z `CurrentValue` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f22e1-167">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="f22e1-168">Když uživatel zadá v textovém poli `onchange` událost se aktivuje a `CurrentValue` je nastavena na hodnotu změněné.</span><span class="sxs-lookup"><span data-stu-id="f22e1-168">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="f22e1-169">Ve skutečnosti je generování kódu poněkud složitější, protože `bind` zpracovává několik případů, kdy jsou provedeny převody typu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-169">In reality, the code generation is a little more complex because `bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="f22e1-170">V zásadě `bind` přidruží aktuální hodnotu výrazu s `value` obslužné rutiny a atributu změny pomocí zaregistrovaná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="f22e1-170">In principle, `bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="f22e1-171">Kromě `onchange`, vlastnost může být vázána pomocí jiné události, jako jsou `oninput` tím, že je explicitní více o tom, co k vytvoření vazby:</span><span class="sxs-lookup"><span data-stu-id="f22e1-171">In addition to `onchange`, the property can be bound using other events like `oninput` by being more explicit about what to bind to:</span></span>

```cshtml
<input type="text" bind-value-oninput="@CurrentValue">
```

<span data-ttu-id="f22e1-172">Na rozdíl od `onchange`, `oninput` aktivována pro každý znak, který je vstup do textového pole.</span><span class="sxs-lookup"><span data-stu-id="f22e1-172">Unlike `onchange`, `oninput` fires for every character that is input into the text box.</span></span>

<span data-ttu-id="f22e1-173">**Formátovací řetězce**</span><span class="sxs-lookup"><span data-stu-id="f22e1-173">**Format strings**</span></span>

<span data-ttu-id="f22e1-174">Vytváření datových vazeb funguje s <xref:System.DateTime> řetězce formátu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-174">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="f22e1-175">V tuto chvíli nejsou k dispozici jiných výrazech formátu, například měny nebo číselných formátů.</span><span class="sxs-lookup"><span data-stu-id="f22e1-175">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd">

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="f22e1-176">`format-value` Atribut specifikuje formát data použít `value` z `input` elementu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-176">The `format-value` attribute specifies the date format to apply to the `value` of the `input` element.</span></span> <span data-ttu-id="f22e1-177">Formát slouží také k analýze hodnotu při `onchange` dojde k události.</span><span class="sxs-lookup"><span data-stu-id="f22e1-177">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="f22e1-178">**Parametry komponenty**</span><span class="sxs-lookup"><span data-stu-id="f22e1-178">**Component parameters**</span></span>

<span data-ttu-id="f22e1-179">Vazba také rozpozná parametry komponenty, kde `bind-{property}` mohl vytvořit vazbu vlastnosti komponentami.</span><span class="sxs-lookup"><span data-stu-id="f22e1-179">Binding also recognizes component parameters, where `bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="f22e1-180">Používá následující komponenty `ChildComponent` a vytvoří vazbu `ParentYear` parametr z nadřazeného `Year` parametru u podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="f22e1-180">The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

<span data-ttu-id="f22e1-181">Nadřazené komponenty:</span><span class="sxs-lookup"><span data-stu-id="f22e1-181">Parent component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="f22e1-182">Podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="f22e1-182">Child component:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

<span data-ttu-id="f22e1-183">Načítají `ParentComponent` vytváří následující značky:</span><span class="sxs-lookup"><span data-stu-id="f22e1-183">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="f22e1-184">Pokud hodnota `ParentYear` vlastnost se změní tak, že vyberete tlačítko v `ParentComponent`, `Year` vlastnost `ChildComponent` se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="f22e1-184">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="f22e1-185">Nová hodnota `Year` se vykreslí v uživatelském rozhraní při `ParentComponent` je rerendered:</span><span class="sxs-lookup"><span data-stu-id="f22e1-185">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="f22e1-186">`Year` Parametr je s možností vazby, protože má doprovodná `YearChanged` událost, která odpovídá typu používaného `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="f22e1-186">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="f22e1-187">Podle konvence `<ChildComponent bind-Year="@ParentYear" />` je v podstatě ekvivalentní zápisu</span><span class="sxs-lookup"><span data-stu-id="f22e1-187">By convention, `<ChildComponent bind-Year="@ParentYear" />` is essentially equivalent to writing,</span></span>

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

<span data-ttu-id="f22e1-188">Obecně platí, vlastnost může být vázána na odpovídající obslužná rutina události pomocí `bind-property-event` atribut.</span><span class="sxs-lookup"><span data-stu-id="f22e1-188">In general, a property can be bound to a corresponding event handler using `bind-property-event` attribute.</span></span>

## <a name="event-handling"></a><span data-ttu-id="f22e1-189">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="f22e1-189">Event handling</span></span>

<span data-ttu-id="f22e1-190">Součásti syntaxe Razor poskytují funkce zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="f22e1-190">Razor components provide event handling features.</span></span> <span data-ttu-id="f22e1-191">Atribut HTML elementu s názvem `on<event>` (například `onclick`, `onsubmit`) s hodnotou typu delegáta Razor součásti považuje za hodnotu atributu obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="f22e1-191">For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="f22e1-192">Název atributu vždy začíná `on`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-192">The attribute's name always starts with `on`.</span></span>

<span data-ttu-id="f22e1-193">Následující kód volá `UpdateHeading` metodu po výběru tlačítka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f22e1-193">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="f22e1-194">Následující kód volá `CheckboxChanged` metoda při změně zaškrtávacího políčka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f22e1-194">The following code calls the `CheckboxChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged">

@functions {
    private void CheckboxChanged()
    {
        ...
    }
}
```

<span data-ttu-id="f22e1-195">Obslužné rutiny událostí může být také asynchronní a zpět <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="f22e1-195">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="f22e1-196">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-196">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="f22e1-197">Výjimky se protokolují, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="f22e1-197">Exceptions are logged when they occur.</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="f22e1-198">Pro některé události nejsou povoleny typy argumentů události specifické pro události.</span><span class="sxs-lookup"><span data-stu-id="f22e1-198">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="f22e1-199">Pokud přístup k jednomu z těchto typů událostí není nezbytné, není potřeba ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="f22e1-199">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="f22e1-200">Seznam podporovaných událostí argumentů je:</span><span class="sxs-lookup"><span data-stu-id="f22e1-200">The list of supported event arguments is:</span></span>

* <span data-ttu-id="f22e1-201">UIEventArgs</span><span class="sxs-lookup"><span data-stu-id="f22e1-201">UIEventArgs</span></span>
* <span data-ttu-id="f22e1-202">UIChangeEventArgs</span><span class="sxs-lookup"><span data-stu-id="f22e1-202">UIChangeEventArgs</span></span>
* <span data-ttu-id="f22e1-203">UIKeyboardEventArgs</span><span class="sxs-lookup"><span data-stu-id="f22e1-203">UIKeyboardEventArgs</span></span>
* <span data-ttu-id="f22e1-204">UIMouseEventArgs</span><span class="sxs-lookup"><span data-stu-id="f22e1-204">UIMouseEventArgs</span></span>

<span data-ttu-id="f22e1-205">Výrazy lambda lze také použít:</span><span class="sxs-lookup"><span data-stu-id="f22e1-205">Lambda expressions can also be used:</span></span>

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="f22e1-206">Často je vhodné zavřít další hodnoty, například během iterace přes sadu elementů.</span><span class="sxs-lookup"><span data-stu-id="f22e1-206">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="f22e1-207">Následující příklad vytvoří tři tlačítka, každý z který volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a tlačítko (`buttonNumber`) při výběru v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f22e1-207">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@functions {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="f22e1-208">Proveďte **není** použít proměnnou smyčky (`i`) v `for` smyčky přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="f22e1-208">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="f22e1-209">V opačném případě se používá stejnou proměnnou všechny výrazy lambda způsobí `i`hodnotu být stejné ve všech výrazů lambda.</span><span class="sxs-lookup"><span data-stu-id="f22e1-209">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="f22e1-210">Zachytit její hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak přes ni.</span><span class="sxs-lookup"><span data-stu-id="f22e1-210">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="f22e1-211">EventCallback</span><span class="sxs-lookup"><span data-stu-id="f22e1-211">EventCallback</span></span>

<span data-ttu-id="f22e1-212">Je běžným scénářem vnořených součástí chce spouštět nadřazenou metodu komponenty, při výskytu události podřízené součásti&mdash;například, když `onclick` v podřízeném dojde k události.</span><span class="sxs-lookup"><span data-stu-id="f22e1-212">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="f22e1-213">Chcete-li zpřístupnit událostí mezi komponentami, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-213">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="f22e1-214">Nadřazené komponenty můžete přiřadit metody zpětného volání k podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-214">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="f22e1-215">Podřízené součásti v ukázkové aplikaci ukazuje, jak tlačítka `onclick` přijímat je nastavena obslužná rutina `EventCallback` delegovat z tohoto příkladu nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-215">The Child component in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's Parent component.</span></span> <span data-ttu-id="f22e1-216">`EventCallback` Je zadán s `UIMouseEventArgs`, což je vhodné pro `onclick` událost z periferní zařízení:</span><span class="sxs-lookup"><span data-stu-id="f22e1-216">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="f22e1-217">Nadřazené komponenty nastaví dítěte `EventCallback<T>` k jeho `ShowMessage` metody:</span><span class="sxs-lookup"><span data-stu-id="f22e1-217">The Parent component sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="f22e1-218">Při výběru tlačítka v podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="f22e1-218">When the button is selected in the Child component:</span></span>

* <span data-ttu-id="f22e1-219">Nadřazené komponenty `ShowMessage` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="f22e1-219">The Parent component's `ShowMessage` method is called.</span></span> <span data-ttu-id="f22e1-220">`messageText` se aktualizují a zobrazí v nadřazené součásti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-220">`messageText` is updated and displayed in the Parent component.</span></span>
* <span data-ttu-id="f22e1-221">Volání `StateHasChanged` není nutné v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="f22e1-221">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="f22e1-222">`StateHasChanged` je volána automaticky k rerender nadřazené komponenty, stejně jako podřízené události aktivovat součást rerendering v obslužných rutinách událostí, které jsou spuštěny v rámci podřízené.</span><span class="sxs-lookup"><span data-stu-id="f22e1-222">`StateHasChanged` is called automatically to rerender the Parent component, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="f22e1-223">`EventCallback` a `EventCallback<T>` povolit asynchronních delegátů.</span><span class="sxs-lookup"><span data-stu-id="f22e1-223">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="f22e1-224">`EventCallback<T>` silně typované a vyžaduje konkrétní argument typu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-224">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="f22e1-225">`EventCallback` slabě typované a jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-225">`EventCallback` is weakly typed and allows any argument type.</span></span>

```chstml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; }" />

@function {
    string messageText;
}
```

<span data-ttu-id="f22e1-226">Vyvolání `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a operátoru await <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="f22e1-226">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="f22e1-227">Použití `EventCallback` a `EventCallback<T>` pro událost zpracování a vazby parametrů komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-227">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span> <span data-ttu-id="f22e1-228">Nepoužívejte `EventCallback` a `EventCallback<T>` pro podřízený obsah&mdash;i nadále používat `RenderFragment` a `RenderFragment<T>` pro podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="f22e1-228">Don't use `EventCallback` and `EventCallback<T>` for child content&mdash;continue to use `RenderFragment` and `RenderFragment<T>` for child content.</span></span>

<span data-ttu-id="f22e1-229">Preferovat silného typu `EventCallback<T>`, která poskytuje lepší odpověď na chybu pro uživatele součásti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-229">Prefer the strongly typed `EventCallback<T>`, which provides better error feedback to users of the component.</span></span> <span data-ttu-id="f22e1-230">Podobně jako jiné obslužné rutině události uživatelského rozhraní, zadání parametru události je volitelné.</span><span class="sxs-lookup"><span data-stu-id="f22e1-230">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="f22e1-231">Použití `EventCallback` při neexistuje převáděná hodnota předaný zpětnému volání.</span><span class="sxs-lookup"><span data-stu-id="f22e1-231">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="f22e1-232">Zachycení odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="f22e1-232">Capture references to components</span></span>

<span data-ttu-id="f22e1-233">Komponenta odkazy poskytují způsob, jak získat odkaz na instanci komponenty tak, aby příkazy do této instance, můžete vydat, jako `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-233">Component references provide a way get a reference to a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="f22e1-234">Chcete-li zachytit odkazem komponenty, přidejte `ref` atribut podřízené součásti a pak definovat pole se stejným názvem a stejného typu jako podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-234">To capture a component reference, add a `ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="f22e1-235">Při vykreslování komponentu `loginDialog` pole se vyplní `MyLoginDialog` podřízené instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-235">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="f22e1-236">Potom můžete vyvolat metody rozhraní .NET na instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-236">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f22e1-237">`loginDialog` Proměnná je vyplněný pouze poté, co se vykreslí komponentu a zahrnuje její výstup `MyLoginDialog` elementu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-237">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="f22e1-238">Až do bodu není nutné nic odkazovat.</span><span class="sxs-lookup"><span data-stu-id="f22e1-238">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="f22e1-239">K manipulaci s odkazy na součásti po dokončení vykreslení komponentu, použijte `OnAfterRenderAsync` nebo `OnAfterRender` metody.</span><span class="sxs-lookup"><span data-stu-id="f22e1-239">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="f22e1-240">Při zachytávání odkazů na komponenty používá podobné syntaxi k [zachytávání odkazy na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) funkce.</span><span class="sxs-lookup"><span data-stu-id="f22e1-240">While capturing component references uses a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="f22e1-241">Nejsou součástí odkazy předané do kódu jazyka JavaScript; se používá pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="f22e1-241">Component references aren't passed to JavaScript code; they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="f22e1-242">Proveďte **není** mutovat stavu podřízenými komponentami pomocí odkazů na komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-242">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="f22e1-243">Místo toho použijte normální deklarované parametry k předání dat podřízenými komponentami.</span><span class="sxs-lookup"><span data-stu-id="f22e1-243">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="f22e1-244">To způsobí, že podřízené součásti rerender ve správném čase automaticky.</span><span class="sxs-lookup"><span data-stu-id="f22e1-244">This causes child components to rerender at the correct times automatically.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="f22e1-245">Životní cyklus metody</span><span class="sxs-lookup"><span data-stu-id="f22e1-245">Lifecycle methods</span></span>

<span data-ttu-id="f22e1-246">`OnInitAsync` a `OnInit` spouštění kódu se inicializovat komponentu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-246">`OnInitAsync` and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="f22e1-247">Chcete-li provádění asynchronní operace, použijte `OnInitAsync` a `await` – klíčové slovo v operaci:</span><span class="sxs-lookup"><span data-stu-id="f22e1-247">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="f22e1-248">Synchronní operace, použijte `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="f22e1-248">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="f22e1-249">`OnParametersSetAsync` a `OnParametersSet` se volá, když součást přijme parametry ze svého nadřazeného objektu a hodnoty jsou přiřazeny k vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="f22e1-249">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="f22e1-250">Tyto metody jsou provedeny po inicializaci součásti a poté je vykreslen komponentu pokaždé, když:</span><span class="sxs-lookup"><span data-stu-id="f22e1-250">These methods are executed after component initialization and then each time the component is rendered:</span></span>

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

<span data-ttu-id="f22e1-251">`OnAfterRenderAsync` a `OnAfterRender` se volá se po vykreslení komponentu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-251">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="f22e1-252">V tomto okamžiku se vyplní elementu a součást odkazy.</span><span class="sxs-lookup"><span data-stu-id="f22e1-252">Element and component references are populated at this point.</span></span> <span data-ttu-id="f22e1-253">Pomocí této fázi můžete provést další inicializaci postup pomocí vykreslovaný obsah, jako je aktivace JavaScript knihovny třetích stran, které pracují s vykreslené elementy modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="f22e1-253">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

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

<span data-ttu-id="f22e1-254">`SetParameters` může být potlačena za účelem spouštění kódu předtím, než jsou nastavené parametry:</span><span class="sxs-lookup"><span data-stu-id="f22e1-254">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="f22e1-255">Pokud `base.SetParameters` není vyvolána, můžete vlastní kód interpretaci příchozí hodnoty parametrů v jakékoli požadované stejně, jako.</span><span class="sxs-lookup"><span data-stu-id="f22e1-255">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="f22e1-256">Například příchozí parametry nejsou potřeba přiřadit k vlastnosti ve třídě.</span><span class="sxs-lookup"><span data-stu-id="f22e1-256">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

<span data-ttu-id="f22e1-257">`ShouldRender` může být potlačena za účelem potlačit aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f22e1-257">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="f22e1-258">Implementace vrátí-li `true`, aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f22e1-258">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="f22e1-259">I když `ShouldRender` je přepsána, komponenta je vždy Počáteční vykreslení.</span><span class="sxs-lookup"><span data-stu-id="f22e1-259">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="f22e1-260">Vyřazení komponenty pomocí rozhraní IDisposable</span><span class="sxs-lookup"><span data-stu-id="f22e1-260">Component disposal with IDisposable</span></span>

<span data-ttu-id="f22e1-261">Pokud komponenta implementuje <xref:System.IDisposable>, [metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) se volá, když je součást z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f22e1-261">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="f22e1-262">Používá následující komponenty `@implements IDisposable` a `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="f22e1-262">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="f22e1-263">Směrování</span><span class="sxs-lookup"><span data-stu-id="f22e1-263">Routing</span></span>

<span data-ttu-id="f22e1-264">Směrování v Blazor se dosahuje tím, že poskytuje šablona trasy pro jednotlivé dostupné komponenty v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f22e1-264">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="f22e1-265">Když soubor Razor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="f22e1-265">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="f22e1-266">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="f22e1-266">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="f22e1-267">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-267">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="f22e1-268">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="f22e1-268">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="f22e1-269">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="f22e1-269">Route parameters</span></span>

<span data-ttu-id="f22e1-270">Součásti mohou přijímat parametry trasy z šablonu trasy, které jsou součástí `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="f22e1-270">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="f22e1-271">Směrovač používá parametry trasy k naplnění odpovídající komponenta parametry.</span><span class="sxs-lookup"><span data-stu-id="f22e1-271">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="f22e1-272">*Komponenta parametr trasa*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-272">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="f22e1-273">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-273">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="f22e1-274">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="f22e1-274">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f22e1-275">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f22e1-275">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="f22e1-276">Základní třída dědičnosti "použití modelu code-behind" prostředí</span><span class="sxs-lookup"><span data-stu-id="f22e1-276">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="f22e1-277">Soubory součástí kombinovat kód HTML a C# zpracování kódu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="f22e1-277">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="f22e1-278">`@inherits` – Direktiva je možné poskytnout Blazor aplikace, který odděluje komponenty značek z kódu pro zpracování prostředí "použití modelu code-behind".</span><span class="sxs-lookup"><span data-stu-id="f22e1-278">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="f22e1-279">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak komponenty může dědit základní třídy `BlazorRocksBase`, aby vznikl komponenty vlastnosti a metody.</span><span class="sxs-lookup"><span data-stu-id="f22e1-279">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="f22e1-280">*Komponenta Blazor Rocks*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-280">*Blazor Rocks component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="f22e1-281">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-281">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="f22e1-282">Základní třída musí být odvozený z: `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-282">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="f22e1-283">Importovat komponenty</span><span class="sxs-lookup"><span data-stu-id="f22e1-283">Import components</span></span>

<span data-ttu-id="f22e1-284">Obor názvů součásti zleva doprava Razor podle:</span><span class="sxs-lookup"><span data-stu-id="f22e1-284">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="f22e1-285">V projektu `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-285">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="f22e1-286">Cesta z kořenového adresáře projektu do komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-286">The path from the project root to the component.</span></span> <span data-ttu-id="f22e1-287">Například `ComponentsSample/Pages/Index.razor` je v oboru názvů `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-287">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="f22e1-288">Postupujte podle součásti C# název pravidel vazby.</span><span class="sxs-lookup"><span data-stu-id="f22e1-288">Components follow C# name binding rules.</span></span> <span data-ttu-id="f22e1-289">V případě třídy *Index.razor*, všechny součásti ve stejné složce *stránky*a nadřazené složky *ComponentsSample*, jsou v oboru.</span><span class="sxs-lookup"><span data-stu-id="f22e1-289">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="f22e1-290">Součásti definované v jiný obor názvů může být přenesena do rozsahu pomocí syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice.</span><span class="sxs-lookup"><span data-stu-id="f22e1-290">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="f22e1-291">Pokud jiná komponenta, `NavMenu.razor`, existuje ve složce `ComponentsSample/Shared/`, součást je možné v `Index.razor` následujícím `@using` – příkaz:</span><span class="sxs-lookup"><span data-stu-id="f22e1-291">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="f22e1-292">Součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, která eliminuje potřebu [ \@pomocí](xref:mvc/views/razor#using) – direktiva:</span><span class="sxs-lookup"><span data-stu-id="f22e1-292">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="f22e1-293">`global::` Kvalifikace se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="f22e1-293">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="f22e1-294">Import součásti s aliasem `using` příkazy (například `@using Foo = Bar`) se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="f22e1-294">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="f22e1-295">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="f22e1-295">Partially qualified names aren't supported.</span></span> <span data-ttu-id="f22e1-296">Například přidáním `@using ComponentsSample` a odkazování na ně `NavMenu.razor` s `<Shared.NavMenu></Shared.NavMenu>` se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="f22e1-296">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="razor-support"></a><span data-ttu-id="f22e1-297">Podpora Razor</span><span class="sxs-lookup"><span data-stu-id="f22e1-297">Razor support</span></span>

<span data-ttu-id="f22e1-298">**Direktivy Razor**</span><span class="sxs-lookup"><span data-stu-id="f22e1-298">**Razor directives**</span></span>

<span data-ttu-id="f22e1-299">V následující tabulce jsou uvedeny direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="f22e1-299">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="f22e1-300">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="f22e1-300">Directive</span></span> | <span data-ttu-id="f22e1-301">Popis</span><span class="sxs-lookup"><span data-stu-id="f22e1-301">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="f22e1-302">\@Funkce</span><span class="sxs-lookup"><span data-stu-id="f22e1-302">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="f22e1-303">Přidá C# blok kódu na komponentu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-303">Adds a C# code block to a component.</span></span> |
| `@implements` | <span data-ttu-id="f22e1-304">Implementuje rozhraní pro třídu vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-304">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="f22e1-305">\@inherits</span><span class="sxs-lookup"><span data-stu-id="f22e1-305">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="f22e1-306">Poskytuje plnou kontrolu nad třídu, která dědí komponentu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-306">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="f22e1-307">\@Vložení</span><span class="sxs-lookup"><span data-stu-id="f22e1-307">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="f22e1-308">Vkládání ze služby umožňuje [kontejneru služby](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f22e1-308">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f22e1-309">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f22e1-309">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="f22e1-310">Určuje komponentu rozložení.</span><span class="sxs-lookup"><span data-stu-id="f22e1-310">Specifies a layout component.</span></span> <span data-ttu-id="f22e1-311">Rozložení komponenty umožňují zabránit zdvojení kódu a nekonzistence.</span><span class="sxs-lookup"><span data-stu-id="f22e1-311">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="f22e1-312">\@Stránka</span><span class="sxs-lookup"><span data-stu-id="f22e1-312">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="f22e1-313">Určuje, že by měla komponenta zpracování požadavků přímo.</span><span class="sxs-lookup"><span data-stu-id="f22e1-313">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="f22e1-314">`@page` – Direktiva je možné zadat při trasy a volitelné parametry.</span><span class="sxs-lookup"><span data-stu-id="f22e1-314">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="f22e1-315">Na rozdíl od Razor Pages `@page` – direktiva nemusí být první – direktiva v horní části souboru.</span><span class="sxs-lookup"><span data-stu-id="f22e1-315">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="f22e1-316">Další informace najdete v tématu [směrování](xref:blazor/routing).</span><span class="sxs-lookup"><span data-stu-id="f22e1-316">For more information, see [Routing](xref:blazor/routing).</span></span> |
| [<span data-ttu-id="f22e1-317">\@použití</span><span class="sxs-lookup"><span data-stu-id="f22e1-317">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="f22e1-318">Přidá C# `using` směrnice do třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-318">Adds the C# `using` directive to the generated component class.</span></span> <span data-ttu-id="f22e1-319">To přináší také všechny součásti, které jsou definované v tomto oboru názvů do oboru.</span><span class="sxs-lookup"><span data-stu-id="f22e1-319">This also brings all the components defined in that namespace into scope.</span></span> |

<span data-ttu-id="f22e1-320">**Podmíněné atributy**</span><span class="sxs-lookup"><span data-stu-id="f22e1-320">**Conditional attributes**</span></span>

<span data-ttu-id="f22e1-321">Atributy jsou vykreslovány podmíněně na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="f22e1-321">Attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="f22e1-322">Pokud je hodnota `false` nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="f22e1-322">If the value is `false` or `null`,  the attribute isn't rendered.</span></span> <span data-ttu-id="f22e1-323">Pokud je hodnota `true`, atribut je vykreslen minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="f22e1-323">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="f22e1-324">V následujícím příkladu `IsCompleted` Určuje, zda `checked` se vykreslí v značky ovládacího prvku:</span><span class="sxs-lookup"><span data-stu-id="f22e1-324">In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted">

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="f22e1-325">Pokud `IsCompleted` je `true`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="f22e1-325">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked>
```

<span data-ttu-id="f22e1-326">Pokud `IsCompleted` je `false`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="f22e1-326">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox">
```

<span data-ttu-id="f22e1-327">**Další informace o syntaxi Razor**</span><span class="sxs-lookup"><span data-stu-id="f22e1-327">**Additional information on Razor**</span></span>

<span data-ttu-id="f22e1-328">Další informace o syntaxi Razor, najdete v článku [referenční příručka syntaxe Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="f22e1-328">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="f22e1-329">Raw HTML</span><span class="sxs-lookup"><span data-stu-id="f22e1-329">Raw HTML</span></span>

<span data-ttu-id="f22e1-330">Řetězce jsou obvykle vykreslen pomocí modelu DOM textové uzly, což znamená, že všechny značky, které mohou obsahovat je ignorována a považována jako prostý text.</span><span class="sxs-lookup"><span data-stu-id="f22e1-330">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="f22e1-331">Pokud chcete zobrazit nezpracovaný kód HTML, zabalit obsah HTML v `MarkupString` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-331">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="f22e1-332">Hodnota je analyzovat ve formátu HTML nebo SVG a vložit do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="f22e1-332">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="f22e1-333">Vykreslování nezpracovaný kód HTML vytvořený z libovolného nedůvěryhodný zdroj je **bezpečnostní riziko** a mělo by se vyhnout!</span><span class="sxs-lookup"><span data-stu-id="f22e1-333">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="f22e1-334">Následující příklad ukazuje použití `MarkupString` typu přidáte blok statického obsahu HTML vykresleného výstupu součásti:</span><span class="sxs-lookup"><span data-stu-id="f22e1-334">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@functions {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="f22e1-335">Bez vizuálního vzhledu součásti</span><span class="sxs-lookup"><span data-stu-id="f22e1-335">Templated components</span></span>

<span data-ttu-id="f22e1-336">Bez vizuálního vzhledu komponenty jsou součástí, které přijímají jeden nebo více šablon uživatelského rozhraní jako parametry, které lze použít jako součást logiky komponenty vykreslování.</span><span class="sxs-lookup"><span data-stu-id="f22e1-336">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="f22e1-337">Bez vizuálního vzhledu komponenty umožňují vytvářet vyšší úrovně součásti, které jsou více než regulární komponenty opakovaně použitelné.</span><span class="sxs-lookup"><span data-stu-id="f22e1-337">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="f22e1-338">Zahrnují několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="f22e1-338">A couple of examples include:</span></span>

* <span data-ttu-id="f22e1-339">Komponenta tabulky, která umožňuje uživateli zadat šablony pro záhlaví tabulky, řádky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="f22e1-339">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="f22e1-340">Seznam součástí, která umožňuje uživateli zadat šablonu pro vykreslování položky v seznamu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-340">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="f22e1-341">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="f22e1-341">Template parameters</span></span>

<span data-ttu-id="f22e1-342">Bez vizuálního vzhledu součásti je definován tak, že zadáte jeden nebo více parametrů součást typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="f22e1-342">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="f22e1-343">Vykreslení fragment reprezentuje segment, který uživatelského rozhraní, které je vykresleno komponentou.</span><span class="sxs-lookup"><span data-stu-id="f22e1-343">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="f22e1-344">Vykreslení fragment volitelně přebírá parametr, který lze zadat, pokud je vyvolána fragment vykreslení.</span><span class="sxs-lookup"><span data-stu-id="f22e1-344">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="f22e1-345">*Tabulka součásti šablony*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-345">*Table Template component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="f22e1-346">Při použití komponenty bez vizuálního vzhledu, parametry šablony lze pomocí podřízené prvky, které odpovídají názvům parametry (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="f22e1-346">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="f22e1-347">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="f22e1-347">Template context parameters</span></span>

<span data-ttu-id="f22e1-348">Komponenta argumenty typu `RenderFragment<T>` předaný jako elementy mají implicitní parametr s názvem `context` (například z předchozí příklad kódu `@context.PetId`), ale můžete změnit pomocí parametru název `Context` atribut na podřízené element.</span><span class="sxs-lookup"><span data-stu-id="f22e1-348">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="f22e1-349">V následujícím příkladu `RowTemplate` elementu `Context` Určuje atribut `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="f22e1-349">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="f22e1-350">Alternativně můžete zadat `Context` atribut na prvek součásti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-350">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="f22e1-351">Zadaný `Context` atributu platí pro všechny parametry určené šablony.</span><span class="sxs-lookup"><span data-stu-id="f22e1-351">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="f22e1-352">To může být užitečné, pokud chcete zadat název obsahu parametru pro implicitní podřízený obsah (bez jakékoli zabalení podřízený element).</span><span class="sxs-lookup"><span data-stu-id="f22e1-352">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="f22e1-353">V následujícím příkladu `Context` atributu se zobrazí na `TableTemplate` elementu a platí pro všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="f22e1-353">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="f22e1-354">Obecné typy komponenty</span><span class="sxs-lookup"><span data-stu-id="f22e1-354">Generic-typed components</span></span>

<span data-ttu-id="f22e1-355">Bez vizuálního vzhledu součásti jsou často obecně typu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-355">Templated components are often generically typed.</span></span> <span data-ttu-id="f22e1-356">Například komponentu obecného seznamu zobrazit šablonu můžete použít k vykreslení `IEnumerable<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-356">For example, a generic List View Template component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="f22e1-357">K definování obecné součásti, použijte `@typeparam` směrnice a určete parametry typu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-357">To define a generic component, use the `@typeparam` directive to specify type parameters.</span></span>

<span data-ttu-id="f22e1-358">*Součásti šablony ListView*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-358">*ListView Template component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="f22e1-359">Při použití komponenty obecného typu, parametr typu je odvozený Pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="f22e1-359">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="f22e1-360">V opačném případě parametr typu musí být explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-360">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="f22e1-361">V následujícím příkladu `TItem="Pet"` Určuje typ:</span><span class="sxs-lookup"><span data-stu-id="f22e1-361">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="f22e1-362">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="f22e1-362">Cascading values and parameters</span></span>

<span data-ttu-id="f22e1-363">V některých případech je nevhodné toku dat z jako součást nadřazené komponenty potomka pomocí [parametry komponenty](#component-parameters), zvlášť pokud máte několik vrstev komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-363">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="f22e1-364">Kaskádové hodnoty a parametry tento problém vyřešit tím, že poskytuje pohodlný způsob pro komponentu předchůdce k zadání hodnoty pro všechny jeho podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-364">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="f22e1-365">Kaskádové hodnoty a parametry také poskytnout přístup pro součásti pro koordinaci.</span><span class="sxs-lookup"><span data-stu-id="f22e1-365">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="f22e1-366">Příklad motiv</span><span class="sxs-lookup"><span data-stu-id="f22e1-366">Theme example</span></span>

<span data-ttu-id="f22e1-367">V následujícím *motiv* příklad z ukázkové aplikace `ThemeInfo` třída určuje informace o motivech tok součástí hierarchií směrem dolů, aby všechna tlačítka v rámci dané části aplikace sdílet stejný styl.</span><span class="sxs-lookup"><span data-stu-id="f22e1-367">In the following *Theme* example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="f22e1-368">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-368">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="f22e1-369">Jako součást předchůdce může poskytnout kaskádové hodnotu pomocí komponenty kaskádové hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-369">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="f22e1-370">Součást CSS hodnoty zabalí podstrom součástí hierarchie a poskytuje jednu hodnotu pro všechny komponenty v rámci této podstrom.</span><span class="sxs-lookup"><span data-stu-id="f22e1-370">The Cascading Value component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="f22e1-371">Například ukázkové aplikace určuje informace o motivech (`ThemeInfo`) v jednom z rozložení aplikace jako parametr šablony pro všechny součásti, které tvoří rozložení textu `@Body` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f22e1-371">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="f22e1-372">`ButtonClass` je přiřazena hodnota `btn-success` v komponentě rozložení.</span><span class="sxs-lookup"><span data-stu-id="f22e1-372">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="f22e1-373">Všechny podřízené součásti mohou využívat tuto vlastnost prostřednictvím `ThemeInfo` šablony objektu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-373">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="f22e1-374">*Kaskádové součást hodnoty parametrů rozložení*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-374">*Cascading Values Parameters Layout component*:</span></span>

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

@functions {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="f22e1-375">Chcete-li pomocí kaskádových hodnot, komponenty deklarovat kaskádové parametry s využitím `[CascadingParameter]` atribut nebo na základě hodnoty názvu řetězec:</span><span class="sxs-lookup"><span data-stu-id="f22e1-375">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="f22e1-376">Vazba s hodnotou řetězce názvu platí, pokud máte více kaskádových hodnot stejného typu a pro jejich odlišení v rámci stejné podstrom.</span><span class="sxs-lookup"><span data-stu-id="f22e1-376">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="f22e1-377">Kaskádové hodnoty se váží k parametrům šablony podle typu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-377">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="f22e1-378">V ukázkové aplikaci, komponentě CSS motiv hodnoty parametrů vytvoří vazbu `ThemeInfo` kaskádové hodnotu pro parametr šablony.</span><span class="sxs-lookup"><span data-stu-id="f22e1-378">In the sample app, the Cascading Values Parameters Theme component binds to the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="f22e1-379">Tento parametr se používá nastavení třídy šablony stylů CSS pro jedno z tlačítek zobrazí komponentou.</span><span class="sxs-lookup"><span data-stu-id="f22e1-379">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="f22e1-380">*Kaskádové součást hodnoty parametrů motiv*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-380">*Cascading Values Parameters Theme component*:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@functions {
    private int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a><span data-ttu-id="f22e1-381">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="f22e1-381">TabSet example</span></span>

<span data-ttu-id="f22e1-382">Parametry šablony také povolit součásti spolupracovat napříč hierarchií komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-382">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="f22e1-383">Představte si třeba následující *TabSet* příklad v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f22e1-383">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="f22e1-384">Ukázková aplikace má `ITab` rozhraní, které karty implementace:</span><span class="sxs-lookup"><span data-stu-id="f22e1-384">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="f22e1-385">Komponenta CSS hodnoty parametrů TabSet používá součásti záložku, která obsahuje několik komponent kartu:</span><span class="sxs-lookup"><span data-stu-id="f22e1-385">The Cascading Values Parameters TabSet component uses the Tab Set component, which contains several Tab components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="f22e1-386">Podřízené kartu komponenty nejsou explicitně předány jako parametry na kartě nastavení.</span><span class="sxs-lookup"><span data-stu-id="f22e1-386">The child Tab components aren't explicitly passed as parameters to the Tab Set.</span></span> <span data-ttu-id="f22e1-387">Místo toho podřízené kartu komponenty jsou součástí podřízenému obsahu ovládacího prvku na kartě nastavení.</span><span class="sxs-lookup"><span data-stu-id="f22e1-387">Instead, the child Tab components are part of the child content of the Tab Set.</span></span> <span data-ttu-id="f22e1-388">Však nastavení kartu stále potřebuje vědět o jednotlivých součástech kartu tak, aby ho může mít za následek záhlaví a na aktivní kartě. Povolit koordinace bez potřeby dalšího kódu, nastavte kartu komponenty *samotný můžete zadat jako hodnotu kaskádové* , který se potom vybere potomka kartu komponenty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-388">However, the Tab Set still needs to know about each Tab component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the Tab Set component *can provide itself as a cascading value* that is then picked up by the descendent Tab components.</span></span>

<span data-ttu-id="f22e1-389">*Komponenta TabSet*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-389">*TabSet component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="f22e1-390">Podřízené součásti zachycení kartu obsahující kartu nastavit jako parametr šablony, karta součásti přidaly na kartu Nastavení a souřadnice na které kartě je aktivní.</span><span class="sxs-lookup"><span data-stu-id="f22e1-390">The descendent Tab components capture the containing Tab Set as a cascading parameter, so the Tab components add themselves to the Tab Set and coordinate on which tab is active.</span></span>

<span data-ttu-id="f22e1-391">*Karta komponenty*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-391">*Tab component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="f22e1-392">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="f22e1-392">Razor templates</span></span>

<span data-ttu-id="f22e1-393">Vykreslení fragmenty lze definovat pomocí syntaxe šablon Razor.</span><span class="sxs-lookup"><span data-stu-id="f22e1-393">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="f22e1-394">Šablony Razor představují způsob, jak definovat fragment kódu uživatelského rozhraní a předpokládají následující formát:</span><span class="sxs-lookup"><span data-stu-id="f22e1-394">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="f22e1-395">Následující příklad ukazuje, jak určit `RenderFragment` a `RenderFragment<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f22e1-395">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="f22e1-396">*Součásti šablony Razor*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-396">*Razor Templates component*:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="f22e1-397">Vykreslení fragmenty definované pomocí syntaxe Razor šablony mohou být předány jako argumenty bez vizuálního vzhledu součásti nebo vykresluje přímo.</span><span class="sxs-lookup"><span data-stu-id="f22e1-397">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="f22e1-398">Předchozí šablony jsou přímo vykreslí následujícím kódem Razor:</span><span class="sxs-lookup"><span data-stu-id="f22e1-398">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="f22e1-399">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="f22e1-399">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="f22e1-400">Ruční RenderTreeBuilder logiky</span><span class="sxs-lookup"><span data-stu-id="f22e1-400">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="f22e1-401">`Microsoft.AspNetCore.Components.RenderTree` poskytuje metody pro manipulaci s komponentami a prvky, včetně sestavení součástí ručně v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-401">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="f22e1-402">Použití `RenderTreeBuilder` vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="f22e1-402">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="f22e1-403">Poškozená součásti (například značku neuzavřený značek) může způsobit nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="f22e1-403">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="f22e1-404">Vezměte v úvahu následující podrobnosti domácí mazlíček součásti, která ručně se dají do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="f22e1-404">Consider the following Pet Details component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="f22e1-405">V následujícím příkladu, smyčky v `CreateComponent` metoda generuje tři komponenty domácí mazlíček podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="f22e1-405">In the following example, the loop in the `CreateComponent` method generates three Pet Details components.</span></span> <span data-ttu-id="f22e1-406">Při volání metody `RenderTreeBuilder` metody vytvoření součásti (`OpenComponent` a `AddAttribute`), pořadová čísla jsou čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="f22e1-406">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="f22e1-407">Algoritmus rozdíl Blazor spoléhá na pořadová čísla odpovídající odlišné řádky kódu, není odlišné volání volání.</span><span class="sxs-lookup"><span data-stu-id="f22e1-407">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="f22e1-408">Při vytváření komponent pomocí `RenderTreeBuilder` metody, pevně argumenty pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="f22e1-408">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="f22e1-409">**Použití výpočtu nebo čítače k vygenerování pořadové číslo může vést ke špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="f22e1-409">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span>

<span data-ttu-id="f22e1-410">*Integrované komponenty obsah*:</span><span class="sxs-lookup"><span data-stu-id="f22e1-410">*Built Content component*:</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" onclick="@RenderComponent">
    Create three Pet Details components
</button>

@functions {
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
