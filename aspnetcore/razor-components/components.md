---
title: Vytváření a používání komponent Razor
author: guardrex
description: Zjistěte, jak vytvořit a používat komponenty Razor, včetně jak vázat na data, zpracování událostí a spravovat životní cykly komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/components
ms.openlocfilehash: 550755d4cc2bd309846e1cc332c3bb1f0b812c25
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668146"
---
# <a name="create-and-use-razor-components"></a><span data-ttu-id="caa3d-103">Vytváření a používání komponent Razor</span><span class="sxs-lookup"><span data-stu-id="caa3d-103">Create and use Razor Components</span></span>

<span data-ttu-id="caa3d-104">Podle [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a [Morné Zaayman](https://github.com/MorneZaayman)</span><span class="sxs-lookup"><span data-stu-id="caa3d-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Morné Zaayman](https://github.com/MorneZaayman)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="caa3d-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="caa3d-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="caa3d-106">Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="caa3d-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="caa3d-107">Razor komponenty aplikace jsou sestaveny na základě *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="caa3d-107">Razor Components apps are built using *components*.</span></span> <span data-ttu-id="caa3d-108">Komponenta je samostatná blok uživatelského rozhraní (UI), například stránky, dialogové okno nebo formuláře.</span><span class="sxs-lookup"><span data-stu-id="caa3d-108">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="caa3d-109">Komponenta obsahuje kód HTML pro vykreslení spolu s logika zpracování potřeba k vložení dat nebo reakce na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="caa3d-109">A component includes both the HTML markup to render along with the processing logic needed to inject data or respond to UI events.</span></span> <span data-ttu-id="caa3d-110">Součásti jsou flexibilní a jednoduchý a mohou být vnořené, znovu použít a sdílet mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-110">Components are flexible and lightweight, and they can be nested, reused, and shared between projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="caa3d-111">Třídy součásti</span><span class="sxs-lookup"><span data-stu-id="caa3d-111">Component classes</span></span>

<span data-ttu-id="caa3d-112">Součásti jsou zpravidla implementovaní v  *\*.cshtml* soubory pomocí kombinace C# a značka jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="caa3d-112">Components are typically implemented in *\*.cshtml* files using a combination of C# and HTML markup.</span></span> <span data-ttu-id="caa3d-113">Uživatelské rozhraní pro součást je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="caa3d-113">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="caa3d-114">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="caa3d-114">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span></span> <span data-ttu-id="caa3d-115">Když je kompilován Razor součásti aplikace, bude značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-115">When a Razor Components app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="caa3d-116">Název generované třídy odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="caa3d-116">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="caa3d-117">Členy třídy komponenty jsou definovány v `@functions` blok (více než jeden `@functions` blok je povolený).</span><span class="sxs-lookup"><span data-stu-id="caa3d-117">Members of the component class are defined in a `@functions` block (more than one `@functions` block is permissible).</span></span> <span data-ttu-id="caa3d-118">V `@functions` bloku, stav komponent (vlastnosti, pole) je zadaný společně s metody pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="caa3d-118">In the `@functions` block, component state (properties, fields) is specified along with methods for event handling or for defining other component logic.</span></span>

<span data-ttu-id="caa3d-119">Komponenta členy můžete poté použita jako tato součást je vykreslování pomocí logiky C# výrazy, které začínají `@`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-119">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="caa3d-120">Například C# pole se vykreslí vložením prefixu `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="caa3d-120">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="caa3d-121">Následující příklad vyhodnotí a vykreslí:</span><span class="sxs-lookup"><span data-stu-id="caa3d-121">The following example evaluates and renders:</span></span>

* <span data-ttu-id="caa3d-122">`_headingFontStyle` Hodnota vlastnosti šablon stylů CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-122">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="caa3d-123">`_headingText` k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-123">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="caa3d-124">Po se zpočátku zobrazí komponentu obnoví komponenty jeho vykreslení stromu v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="caa3d-124">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="caa3d-125">Součásti Razor poté porovnává větve vykreslení oproti předchozímu a platí všechny změny do prohlížeče Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="caa3d-125">Razor Components then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

## <a name="using-components"></a><span data-ttu-id="caa3d-126">Pomocí komponent</span><span class="sxs-lookup"><span data-stu-id="caa3d-126">Using components</span></span>

<span data-ttu-id="caa3d-127">Součásti můžete zahrnout další součásti je deklarací pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="caa3d-127">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="caa3d-128">Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.</span><span class="sxs-lookup"><span data-stu-id="caa3d-128">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="caa3d-129">Následující kód vykreslí `HeadingComponent` (*HeadingComponent.cshtml*) instance:</span><span class="sxs-lookup"><span data-stu-id="caa3d-129">The following markup renders a `HeadingComponent` (*HeadingComponent.cshtml*) instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?start=11&end=11)]

## <a name="component-parameters"></a><span data-ttu-id="caa3d-130">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="caa3d-130">Component parameters</span></span>

<span data-ttu-id="caa3d-131">Může mít komponenty *parametry komponenty*, které jsou definovány pomocí *neveřejné* vlastnosti komponentní třída upravené pomocí `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-131">Components can have *component parameters*, which are defined using *non-public* properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="caa3d-132">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-132">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="caa3d-133">V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` vlastnost `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="caa3d-133">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`:</span></span>

<span data-ttu-id="caa3d-134">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-134">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?start=1&end=7&highlight=5)]

<span data-ttu-id="caa3d-135">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-135">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a><span data-ttu-id="caa3d-136">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="caa3d-136">Child content</span></span>

<span data-ttu-id="caa3d-137">Součásti můžete nastavení obsahu v jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="caa3d-137">Components can set the content in another component.</span></span> <span data-ttu-id="caa3d-138">Přiřazení součásti najdete zde obsah mezi značky, které určují přijímající komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-138">The assigning component provides the content between the tags that specify the receiving component.</span></span> <span data-ttu-id="caa3d-139">Například `ParentComponent` poskytnete obsah, který má být vykreslen pomocí `ChildComponent` tak, že je obsah uvnitř  **\<ChildComponent >** značky.</span><span class="sxs-lookup"><span data-stu-id="caa3d-139">For example, a `ParentComponent` can provide content that is to be rendered by a `ChildComponent` by placing the content inside **\<ChildComponent>** tags.</span></span>

<span data-ttu-id="caa3d-140">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-140">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?start=1&end=7&highlight=6)]

<span data-ttu-id="caa3d-141">Obsahuje podřízené součásti `ChildContent` vlastnost, která představuje `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-141">The child component has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="caa3d-142">Hodnota `ChildContent` je umístěn ve značkách podřízené součásti, kde má být vykreslen obsah.</span><span class="sxs-lookup"><span data-stu-id="caa3d-142">The value of `ChildContent` is positioned in the child component's markup where the content should be rendered.</span></span> <span data-ttu-id="caa3d-143">V následujícím příkladu, hodnota `ChildContent` přijme od nadřazené komponenty a vykreslit v rámci panelu Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-143">In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="caa3d-144">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-144">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> <span data-ttu-id="caa3d-145">Vlastnost příjmu `RenderFragment` obsahu musí mít název `ChildContent` konvencí.</span><span class="sxs-lookup"><span data-stu-id="caa3d-145">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

## <a name="data-binding"></a><span data-ttu-id="caa3d-146">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="caa3d-146">Data binding</span></span>

<span data-ttu-id="caa3d-147">Vazba dat na komponent a prvky modelu DOM se dosahuje pomocí `bind` atribut.</span><span class="sxs-lookup"><span data-stu-id="caa3d-147">Data binding to both components and DOM elements is accomplished with the `bind` attribute.</span></span> <span data-ttu-id="caa3d-148">Následující příklad vytvoří vazbu `ItalicsCheck` vlastnost na zaškrtávací políčko zaškrtnuto, stav:</span><span class="sxs-lookup"><span data-stu-id="caa3d-148">The following example binds the `ItalicsCheck` property to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" bind="@_italicsCheck" />
```

<span data-ttu-id="caa3d-149">Při zaškrtnutí políčka je a zrušte zaškrtnutí, hodnota vlastnosti je aktualizována na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="caa3d-149">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="caa3d-150">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní, pouze v případě, že součást je vykresleno, ne v reakci na měnící se hodnota vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="caa3d-150">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="caa3d-151">Protože komponenty vykreslování sami po spuštění kódu obslužné rutiny události, aktualizace vlastností se obvykle projeví v uživatelském rozhraní ihned.</span><span class="sxs-lookup"><span data-stu-id="caa3d-151">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="caa3d-152">Pomocí `bind` s `CurrentValue` vlastnosti (`<input bind="@CurrentValue" />`) je v podstatě ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="caa3d-152">Using `bind` with a `CurrentValue` property (`<input bind="@CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="caa3d-153">Při vykreslování komponentu `value` elementu input pochází z `CurrentValue` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="caa3d-153">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="caa3d-154">Když uživatel zadá v textovém poli `onchange` se aktivuje a `CurrentValue` je nastavena na hodnotu změněné.</span><span class="sxs-lookup"><span data-stu-id="caa3d-154">When the user types in the text box, the `onchange` is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="caa3d-155">Ve skutečnosti je generování kódu poněkud složitější, protože `bind` se zabývá několik případů, kdy jsou provedeny převody typu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-155">In reality, the code generation is a little more complex because `bind` deals with a few cases where type conversions are performed.</span></span> <span data-ttu-id="caa3d-156">V zásadě `bind` přidruží aktuální hodnotu výrazu s `value` obslužné rutiny a atributu změny pomocí zaregistrovaná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="caa3d-156">In principle, `bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="caa3d-157">**Formátovací řetězce**</span><span class="sxs-lookup"><span data-stu-id="caa3d-157">**Format strings**</span></span>

<span data-ttu-id="caa3d-158">Vytváření datových vazeb funguje s [data a času](https://docs.microsoft.com/dotnet/api/system.datetime) formátování řetězce (ale ne jiných výrazech formátu v tuto chvíli, jako je například měnu ani číselných formátů):</span><span class="sxs-lookup"><span data-stu-id="caa3d-158">Data binding works with [DateTime](https://docs.microsoft.com/dotnet/api/system.datetime) format strings (but not other format expressions at this time, such as currency or number formats):</span></span>

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd" />

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="caa3d-159">`format-value` Atribut specifikuje formát data použít `value` z `input` elementu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-159">The `format-value` attribute specifies the date format to apply to the `value` of the `input` element.</span></span> <span data-ttu-id="caa3d-160">Formát slouží také k analýze hodnotu při `onchange` dojde k události.</span><span class="sxs-lookup"><span data-stu-id="caa3d-160">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="caa3d-161">**Parametry komponenty**</span><span class="sxs-lookup"><span data-stu-id="caa3d-161">**Component parameters**</span></span>

<span data-ttu-id="caa3d-162">Vazba také rozpozná parametry komponenty, kde `bind-{property}` mohl vytvořit vazbu vlastnosti komponentami.</span><span class="sxs-lookup"><span data-stu-id="caa3d-162">Binding also recognizes component parameters, where `bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="caa3d-163">Používá následující komponenty `ChildComponent` a vytvoří vazbu `ParentYear` parametr z nadřazeného `Year` parametru u podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="caa3d-163">The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

<span data-ttu-id="caa3d-164">Nadřazené komponenty:</span><span class="sxs-lookup"><span data-stu-id="caa3d-164">Parent component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">Change Year to 1986</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="caa3d-165">Podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="caa3d-165">Child component:</span></span>

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

<span data-ttu-id="caa3d-166">`Year` Parametr je s možností vazby, protože má doprovodná `YearChanged` událost, která odpovídá typu používaného `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="caa3d-166">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="caa3d-167">Načítají `ParentComponent` vytváří následující značky:</span><span class="sxs-lookup"><span data-stu-id="caa3d-167">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="caa3d-168">Pokud hodnota `ParentYear` vlastnost se změní tak, že vyberete tlačítko v `ParentComponent`, `Year` vlastnost `ChildComponent` se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="caa3d-168">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="caa3d-169">Nová hodnota `Year` se vykreslí v uživatelském rozhraní při `ParentComponent` je rerendered:</span><span class="sxs-lookup"><span data-stu-id="caa3d-169">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

## <a name="event-handling"></a><span data-ttu-id="caa3d-170">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="caa3d-170">Event handling</span></span>

<span data-ttu-id="caa3d-171">Součásti syntaxe Razor poskytují funkce zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="caa3d-171">Razor Components provide event handling features.</span></span> <span data-ttu-id="caa3d-172">Atribut HTML elementu s názvem `on<event>` (například `onclick`, `onsubmit`) s hodnotou typu delegáta Razor součásti považuje za hodnotu atributu obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="caa3d-172">For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Razor Components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="caa3d-173">Název atributu vždy začíná `on`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-173">The attribute's name always starts with `on`.</span></span>

<span data-ttu-id="caa3d-174">Následující kód volá `UpdateHeading` metodu po výběru tlačítka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="caa3d-174">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="caa3d-175">Následující kód volá `CheckboxChanged` metoda při změně zaškrtávacího políčka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="caa3d-175">The following code calls the `CheckboxChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged" />

@functions {
    void CheckboxChanged()
    {
        ...
    }
}
```

<span data-ttu-id="caa3d-176">Obslužné rutiny událostí může být také asynchronní a zpět `Task`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-176">Event handlers can also be asynchronous and return a `Task`.</span></span> <span data-ttu-id="caa3d-177">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-177">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="caa3d-178">Výjimky se protokolují, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="caa3d-178">Exceptions are logged when they occur.</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="caa3d-179">Pro některé události nejsou povoleny typy argumentů události specifické pro události.</span><span class="sxs-lookup"><span data-stu-id="caa3d-179">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="caa3d-180">Pokud přístup k jednomu z těchto typů událostí není nezbytné, není potřeba ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="caa3d-180">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="caa3d-181">Seznam podporovaných událostí argumentů je:</span><span class="sxs-lookup"><span data-stu-id="caa3d-181">The list of supported event arguments is:</span></span>

* <span data-ttu-id="caa3d-182">UIEventArgs</span><span class="sxs-lookup"><span data-stu-id="caa3d-182">UIEventArgs</span></span>
* <span data-ttu-id="caa3d-183">UIChangeEventArgs</span><span class="sxs-lookup"><span data-stu-id="caa3d-183">UIChangeEventArgs</span></span>
* <span data-ttu-id="caa3d-184">UIKeyboardEventArgs</span><span class="sxs-lookup"><span data-stu-id="caa3d-184">UIKeyboardEventArgs</span></span>
* <span data-ttu-id="caa3d-185">UIMouseEventArgs</span><span class="sxs-lookup"><span data-stu-id="caa3d-185">UIMouseEventArgs</span></span>

<span data-ttu-id="caa3d-186">Výrazy lambda lze také použít:</span><span class="sxs-lookup"><span data-stu-id="caa3d-186">Lambda expressions can also be used:</span></span>

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="caa3d-187">Často je vhodné zavřít další hodnoty, například během iterace přes sadu elementů.</span><span class="sxs-lookup"><span data-stu-id="caa3d-187">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="caa3d-188">Následující příklad vytvoří tři tlačítka, každý z který volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a tlačítko (`buttonNumber`) při výběru v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="caa3d-188">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
    string message = "Select a button to learn its position.";

    void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="caa3d-189">Zachycení odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="caa3d-189">Capture references to components</span></span>

<span data-ttu-id="caa3d-190">Komponenta odkazy poskytují způsob, jak získat odkaz na instanci komponenty tak, aby příkazy do této instance, můžete vydat, jako `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-190">Component references provide a way get a reference to a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="caa3d-191">Chcete-li zachytit odkazem komponenty, přidejte `ref` atribut podřízené součásti a pak definovat pole se stejným názvem a stejného typu jako podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="caa3d-191">To capture a component reference, add a `ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    MyLoginDialog loginDialog;

    void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="caa3d-192">Při vykreslování komponentu `loginDialog` pole se vyplní `MyLoginDialog` podřízené instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-192">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="caa3d-193">Potom můžete vyvolat metody rozhraní .NET na instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-193">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="caa3d-194">`loginDialog` Proměnná je vyplněný pouze poté, co se vykreslí komponentu a zahrnuje její výstup `MyLoginDialog` element vzhledem k tomu, že do té doby se nedá nic odkazovat.</span><span class="sxs-lookup"><span data-stu-id="caa3d-194">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element because until then there is nothing to reference.</span></span> <span data-ttu-id="caa3d-195">K manipulaci s odkazy na součásti po dokončení vykreslení komponentu, použijte `OnAfterRenderAsync` nebo `OnAfterRender` životního cyklu metody.</span><span class="sxs-lookup"><span data-stu-id="caa3d-195">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` lifecycle methods.</span></span>

<span data-ttu-id="caa3d-196">Při zachytávání odkazů na komponenty používá podobné syntaxi k [zachytávání odkazy na prvky](xref:razor-components/javascript-interop#capture-references-to-elements), není [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop) funkce.</span><span class="sxs-lookup"><span data-stu-id="caa3d-196">While capturing component references uses a similar syntax to [capturing element references](xref:razor-components/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:razor-components/javascript-interop) feature.</span></span> <span data-ttu-id="caa3d-197">Nejsou součástí odkazy předané do kódu jazyka JavaScript; se používá pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="caa3d-197">Component references aren't passed to JavaScript code; they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="caa3d-198">Proveďte **není** mutovat stavu podřízenými komponentami pomocí odkazů na komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-198">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="caa3d-199">Vždy použijte normální deklarované parametry k předání dat podřízenými komponentami.</span><span class="sxs-lookup"><span data-stu-id="caa3d-199">Instead, always use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="caa3d-200">To způsobí, že podřízené součásti rerender ve správném čase automaticky.</span><span class="sxs-lookup"><span data-stu-id="caa3d-200">This causes child components to rerender at the correct times automatically.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="caa3d-201">Životní cyklus metody</span><span class="sxs-lookup"><span data-stu-id="caa3d-201">Lifecycle methods</span></span>

<span data-ttu-id="caa3d-202">`OnInitAsync` a `OnInit` spuštění kódu po inicializaci komponentu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-202">`OnInitAsync` and `OnInit` execute code after the component has been initialized.</span></span> <span data-ttu-id="caa3d-203">Chcete-li provádění asynchronní operace, použijte `OnInitAsync` a použít `await` – klíčové slovo:</span><span class="sxs-lookup"><span data-stu-id="caa3d-203">To perform an asynchronous operation, use `OnInitAsync` and use the `await` keyword:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="caa3d-204">Synchronní operace, použijte `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="caa3d-204">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="caa3d-205">`OnParametersSetAsync` a `OnParametersSet` se volá, když součást přijme parametry ze svého nadřazeného objektu a hodnoty jsou přiřazeny k vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="caa3d-205">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="caa3d-206">Tyto metody se spustí po `OnInit` při inicializaci součásti.</span><span class="sxs-lookup"><span data-stu-id="caa3d-206">These methods are executed after `OnInit` during component initialization.</span></span>

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

<span data-ttu-id="caa3d-207">`OnAfterRenderAsync` a `OnAfterRender` se nazývají pokaždé, když po dokončení vykreslení komponentu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-207">`OnAfterRenderAsync` and `OnAfterRender` are called each time after a component has finished rendering.</span></span> <span data-ttu-id="caa3d-208">V tomto okamžiku se vyplní elementu a součást odkazy.</span><span class="sxs-lookup"><span data-stu-id="caa3d-208">Element and component references are populated at this point.</span></span> <span data-ttu-id="caa3d-209">Pomocí této fázi můžete provést další inicializaci postup pomocí vykreslovaný obsah, jako je aktivace JavaScript knihovny třetích stran, které pracují s vykreslené elementy modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="caa3d-209">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

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

<span data-ttu-id="caa3d-210">`SetParameters` může být potlačena za účelem spouštění kódu předtím, než jsou nastavené parametry:</span><span class="sxs-lookup"><span data-stu-id="caa3d-210">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="caa3d-211">Pokud `base.SetParameters` není vyvolána, můžete vlastní kód interpretaci příchozí hodnoty parametrů v jakékoli požadované stejně, jako.</span><span class="sxs-lookup"><span data-stu-id="caa3d-211">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="caa3d-212">Například příchozí parametry nejsou potřeba přiřadit k vlastnosti ve třídě.</span><span class="sxs-lookup"><span data-stu-id="caa3d-212">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

<span data-ttu-id="caa3d-213">`ShouldRender` může být potlačena za účelem potlačit aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="caa3d-213">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="caa3d-214">Implementace vrátí-li `true`, aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="caa3d-214">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="caa3d-215">I když `ShouldRender` je přepsána, komponenta je vždy Počáteční vykreslení.</span><span class="sxs-lookup"><span data-stu-id="caa3d-215">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="caa3d-216">Vyřazení komponenty pomocí rozhraní IDisposable</span><span class="sxs-lookup"><span data-stu-id="caa3d-216">Component disposal with IDisposable</span></span>

<span data-ttu-id="caa3d-217">Pokud komponenta implementuje [IDisposable](https://docs.microsoft.com/dotnet/api/system.idisposable), [metoda Dispose](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose) se volá, když je součást z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="caa3d-217">If a component implements [IDisposable](https://docs.microsoft.com/dotnet/api/system.idisposable), the [Dispose method](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="caa3d-218">Používá následující komponenty `@implements IDisposable` a `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="caa3d-218">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="caa3d-219">Směrování</span><span class="sxs-lookup"><span data-stu-id="caa3d-219">Routing</span></span>

<span data-ttu-id="caa3d-220">Směrování v součástech Razor se dosahuje tím, že poskytuje šablona trasy pro jednotlivé dostupné komponenty v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="caa3d-220">Routing in Razor Components is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="caa3d-221">Při  *\*.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy [RouteAttribute](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="caa3d-221">When a *\*.cshtml* file with an `@page` directive is compiled, the generated class is given a [RouteAttribute](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) specifying the route template.</span></span> <span data-ttu-id="caa3d-222">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="caa3d-222">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="caa3d-223">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-223">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="caa3d-224">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="caa3d-224">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?start=1&end=4)]

## <a name="route-parameters"></a><span data-ttu-id="caa3d-225">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="caa3d-225">Route parameters</span></span>

<span data-ttu-id="caa3d-226">Součásti mohou přijímat parametry trasy z šablonu trasy, které jsou součástí `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="caa3d-226">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="caa3d-227">Směrovač používá parametry trasy k naplnění odpovídající komponenta parametry.</span><span class="sxs-lookup"><span data-stu-id="caa3d-227">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="caa3d-228">*RouteParameter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-228">*RouteParameter.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?start=1&end=9)]

<span data-ttu-id="caa3d-229">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-229">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="caa3d-230">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="caa3d-230">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="caa3d-231">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="caa3d-231">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="caa3d-232">Základní třída dědičnosti "použití modelu code-behind" prostředí</span><span class="sxs-lookup"><span data-stu-id="caa3d-232">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="caa3d-233">Soubory komponent (*\*.cshtml*) kombinovat kód HTML a C# zpracování kódu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="caa3d-233">Component files (*\*.cshtml*) mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="caa3d-234">`@inherits` – Direktiva je možné poskytovat prostředí "použití modelu code-behind", který odděluje komponenty značek z zpracování kódu Razor součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="caa3d-234">The `@inherits` directive can be used to provide Razor Components apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="caa3d-235">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ukazuje, jak komponenty může dědit základní třídy `BlazorRocksBase`, aby vznikl komponenty vlastnosti a metody.</span><span class="sxs-lookup"><span data-stu-id="caa3d-235">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="caa3d-236">*BlazorRocks.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-236">*BlazorRocks.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?start=1&end=8)]

<span data-ttu-id="caa3d-237">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-237">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="caa3d-238">Základní třída musí být odvozený z: `BlazorComponent`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-238">The base class should derive from `BlazorComponent`.</span></span>

## <a name="razor-support"></a><span data-ttu-id="caa3d-239">Podpora Razor</span><span class="sxs-lookup"><span data-stu-id="caa3d-239">Razor support</span></span>

<span data-ttu-id="caa3d-240">**Direktivy Razor**</span><span class="sxs-lookup"><span data-stu-id="caa3d-240">**Razor directives**</span></span>

<span data-ttu-id="caa3d-241">V následující tabulce jsou uvedeny direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="caa3d-241">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="caa3d-242">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="caa3d-242">Directive</span></span> | <span data-ttu-id="caa3d-243">Popis</span><span class="sxs-lookup"><span data-stu-id="caa3d-243">Description</span></span> |
| --------- | ----------- |
| [@functions](https://docs.microsoft.com/aspnet/core/mvc/views/razor#functions) | <span data-ttu-id="caa3d-244">Přidá C# blok kódu na komponentu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-244">Adds a C# code block to a component.</span></span> |
| `@implements` | <span data-ttu-id="caa3d-245">Implementuje rozhraní pro třídu vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-245">Implements an interface for the generated component class.</span></span> |
| [@inherits](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inherits) | <span data-ttu-id="caa3d-246">Poskytuje plnou kontrolu nad třídu, která dědí komponentu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-246">Provides full control of the class that the component inherits.</span></span> |
| [@inject](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inject) | <span data-ttu-id="caa3d-247">Vkládání ze služby umožňuje [kontejneru služby](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="caa3d-247">Enables service injection from the [service container](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).</span></span> <span data-ttu-id="caa3d-248">Další informace najdete v tématu [injektáž závislostí do zobrazení](https://docs.microsoft.com/aspnet/core/mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="caa3d-248">For more information, see [Dependency injection into views](https://docs.microsoft.com/aspnet/core/mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="caa3d-249">Určuje komponentu rozložení.</span><span class="sxs-lookup"><span data-stu-id="caa3d-249">Specifies a layout component.</span></span> <span data-ttu-id="caa3d-250">Rozložení komponenty umožňují zabránit zdvojení kódu a nekonzistence.</span><span class="sxs-lookup"><span data-stu-id="caa3d-250">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [@page](https://docs.microsoft.com/aspnet/core/mvc/razor-pages#razor-pages) | <span data-ttu-id="caa3d-251">Určuje, že by měla komponenta zpracování požadavků přímo.</span><span class="sxs-lookup"><span data-stu-id="caa3d-251">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="caa3d-252">`@page` – Direktiva je možné zadat při trasy a volitelné parametry.</span><span class="sxs-lookup"><span data-stu-id="caa3d-252">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="caa3d-253">Na rozdíl od Razor Pages `@page` – direktiva nemusí být první – direktiva v horní části souboru.</span><span class="sxs-lookup"><span data-stu-id="caa3d-253">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="caa3d-254">Další informace najdete v tématu [směrování](xref:razor-components/routing).</span><span class="sxs-lookup"><span data-stu-id="caa3d-254">For more information, see [Routing](xref:razor-components/routing).</span></span> |
| [@using](https://docs.microsoft.com/aspnet/core/mvc/views/razor#using) | <span data-ttu-id="caa3d-255">Přidá C# `using` směrnice do třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-255">Adds the C# `using` directive to the generated component class.</span></span> |
| [@addTagHelper](https://docs.microsoft.com/aspnet/core/mvc/views/razor#tag-helpers) | <span data-ttu-id="caa3d-256">Použít `@addTagHelper` použít komponentu v jiném sestavení než sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="caa3d-256">Use `@addTagHelper` to use a component in a different assembly than the app's assembly.</span></span> |

<span data-ttu-id="caa3d-257">**Podmíněné atributy**</span><span class="sxs-lookup"><span data-stu-id="caa3d-257">**Conditional attributes**</span></span>

<span data-ttu-id="caa3d-258">Atributy jsou vykreslovány podmíněně na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="caa3d-258">Attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="caa3d-259">Pokud je hodnota `false` nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="caa3d-259">If the value is `false` or `null`,  the attribute isn't rendered.</span></span> <span data-ttu-id="caa3d-260">Pokud je hodnota `true`, atribut je vykreslen minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="caa3d-260">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="caa3d-261">V následujícím příkladu `IsCompleted` Určuje, zda `checked` se vykreslí v značky ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="caa3d-261">In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup.</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="caa3d-262">Pokud `IsCompleted` je `true`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="caa3d-262">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="caa3d-263">Pokud `IsCompleted` je `false`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="caa3d-263">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="caa3d-264">**Další informace o syntaxi Razor**</span><span class="sxs-lookup"><span data-stu-id="caa3d-264">**Additional information on Razor**</span></span>

<span data-ttu-id="caa3d-265">Další informace o syntaxi Razor, najdete v článku [referenční příručka syntaxe Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="caa3d-265">For more information on Razor, see the [Razor syntax reference](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span></span> <span data-ttu-id="caa3d-266">Všimněte si, že ne všechny funkce Razor jsou k dispozici v součástech Razor v tuto chvíli.</span><span class="sxs-lookup"><span data-stu-id="caa3d-266">Note that not all of the features of Razor are available in Razor Components at this time.</span></span>

## <a name="raw-html"></a><span data-ttu-id="caa3d-267">Raw HTML</span><span class="sxs-lookup"><span data-stu-id="caa3d-267">Raw HTML</span></span>

<span data-ttu-id="caa3d-268">Řetězce jsou obvykle vykreslen pomocí modelu DOM textové uzly, což znamená, že všechny značky, které mohou obsahovat je ignorována a považována jako prostý text.</span><span class="sxs-lookup"><span data-stu-id="caa3d-268">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="caa3d-269">Pokud chcete zobrazit nezpracovaný kód HTML, zabalit obsah HTML v `MarkupString` hodnotu, která je pak analyzovat ve formátu HTML nebo SVG a vložit do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="caa3d-269">To render raw HTML, wrap the HTML content in a `MarkupString` value, which is then parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="caa3d-270">Vykreslování nezpracovaný kód HTML vytvořený z libovolného nedůvěryhodný zdroj je **bezpečnostní riziko** a mělo by se vyhnout!</span><span class="sxs-lookup"><span data-stu-id="caa3d-270">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="caa3d-271">Následující příklad ukazuje použití `MarkupString` typu přidáte blok statického obsahu HTML vykresleného výstupu součásti:</span><span class="sxs-lookup"><span data-stu-id="caa3d-271">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@functions {
    string myMarkup = "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="caa3d-272">Bez vizuálního vzhledu součásti</span><span class="sxs-lookup"><span data-stu-id="caa3d-272">Templated components</span></span>

<span data-ttu-id="caa3d-273">Bez vizuálního vzhledu komponenty jsou součástí, které přijímají jeden nebo více šablon uživatelského rozhraní jako parametry, které lze použít jako součást logiky komponenty vykreslování.</span><span class="sxs-lookup"><span data-stu-id="caa3d-273">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="caa3d-274">Bez vizuálního vzhledu komponenty umožňují vytvářet vyšší úrovně součásti, které jsou více než regulární komponenty opakovaně použitelné.</span><span class="sxs-lookup"><span data-stu-id="caa3d-274">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="caa3d-275">Zahrnují několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="caa3d-275">A couple of examples include:</span></span>

* <span data-ttu-id="caa3d-276">Komponenta tabulky, která umožňuje uživateli zadat šablony pro záhlaví tabulky, řádky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="caa3d-276">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="caa3d-277">Seznam součástí, která umožňuje uživateli zadat šablonu pro vykreslování položky v seznamu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-277">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="caa3d-278">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="caa3d-278">Template parameters</span></span>

<span data-ttu-id="caa3d-279">Bez vizuálního vzhledu součásti je definován tak, že zadáte jeden nebo více parametrů součást typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-279">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="caa3d-280">Vykreslení fragment reprezentuje segment, který uživatelského rozhraní, které je vykresleno komponentou.</span><span class="sxs-lookup"><span data-stu-id="caa3d-280">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="caa3d-281">Vykreslení fragment volitelně přebírá parametr, který lze zadat, pokud je vyvolána fragment vykreslení.</span><span class="sxs-lookup"><span data-stu-id="caa3d-281">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="caa3d-282">*Components/TableTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-282">*Components/TableTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

<span data-ttu-id="caa3d-283">Při použití komponenty bez vizuálního vzhledu, parametry šablony lze pomocí podřízené prvky, které odpovídají názvům parametry (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="caa3d-283">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="caa3d-284">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="caa3d-284">Template context parameters</span></span>

<span data-ttu-id="caa3d-285">Komponenta argumenty typu `RenderFragment<T>` předaný jako elementy mají implicitní parametr s názvem `context` (například z předchozí příklad kódu `@context.PetId`), ale můžete změnit pomocí parametru název `Context` atribut na podřízené element.</span><span class="sxs-lookup"><span data-stu-id="caa3d-285">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="caa3d-286">V následujícím příkladu `RowTemplate` elementu `Context` Určuje atribut `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="caa3d-286">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="caa3d-287">Alternativně můžete zadat `Context` atribut na prvek součásti.</span><span class="sxs-lookup"><span data-stu-id="caa3d-287">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="caa3d-288">Zadaný `Context` atributu platí pro všechny parametry určené šablony.</span><span class="sxs-lookup"><span data-stu-id="caa3d-288">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="caa3d-289">To může být užitečné, pokud chcete zadat název obsahu parametru pro implicitní podřízený obsah (bez jakékoli zabalení podřízený element).</span><span class="sxs-lookup"><span data-stu-id="caa3d-289">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="caa3d-290">V následujícím příkladu `Context` atributu se zobrazí na `TableTemplate` elementu a platí pro všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="caa3d-290">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="caa3d-291">Obecné typy komponenty</span><span class="sxs-lookup"><span data-stu-id="caa3d-291">Generic-typed components</span></span>

<span data-ttu-id="caa3d-292">Bez vizuálního vzhledu součásti jsou často obecně typu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-292">Templated components are often generically typed.</span></span> <span data-ttu-id="caa3d-293">Obecná součást ListView můžete například použít k vykreslení `IEnumerable<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-293">For example, a generic ListView component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="caa3d-294">K definování obecné součásti, použijte `@typeparam` směrnice a určete parametry typu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-294">To define a generic component, use the `@typeparam` directive to specify type parameters.</span></span>

<span data-ttu-id="caa3d-295">*Components/ListViewTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-295">*Components/ListViewTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml?highlight=1)]

<span data-ttu-id="caa3d-296">Při použití komponenty obecného typu, parametr typu je odvozený Pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="caa3d-296">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="caa3d-297">V opačném případě parametr typu musí být explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-297">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="caa3d-298">V následujícím příkladu `TItem="Pet"` Určuje typ:</span><span class="sxs-lookup"><span data-stu-id="caa3d-298">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="caa3d-299">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="caa3d-299">Cascading values and parameters</span></span>

<span data-ttu-id="caa3d-300">V některých případech je nevhodné toku dat z jako součást nadřazené komponenty potomka pomocí [parametry komponenty](#component-parameters), zvlášť pokud máte několik vrstev komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-300">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="caa3d-301">Kaskádové hodnoty a parametry tento problém vyřešit tím, že poskytuje pohodlný způsob pro komponentu předchůdce k zadání hodnoty pro všechny jeho podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="caa3d-301">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="caa3d-302">Kaskádové hodnoty a parametry také poskytnout přístup pro součásti pro koordinaci.</span><span class="sxs-lookup"><span data-stu-id="caa3d-302">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="caa3d-303">Příklad motiv</span><span class="sxs-lookup"><span data-stu-id="caa3d-303">Theme example</span></span>

<span data-ttu-id="caa3d-304">V následujícím *motiv* příklad z ukázkové aplikace `ThemeInfo` třída určuje informace o motivech tok součástí hierarchií směrem dolů, aby všechna tlačítka v rámci dané části aplikace sdílet stejný styl.</span><span class="sxs-lookup"><span data-stu-id="caa3d-304">In the following *Theme* example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="caa3d-305">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-305">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="caa3d-306">Jako součást předchůdce může poskytnout kaskádové hodnotu pomocí `CascadingValue` komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-306">An ancestor component can provide a cascading value using the `CascadingValue` component.</span></span> <span data-ttu-id="caa3d-307">`CascadingValue` Komponenty zabalí podstrom součástí hierarchie a poskytuje jednu hodnotu pro všechny komponenty v rámci této podstrom.</span><span class="sxs-lookup"><span data-stu-id="caa3d-307">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="caa3d-308">Například ukázkové aplikace určuje informace o motivech (`ThemeInfo`) v jednom z rozložení aplikace jako parametr šablony pro všechny součásti, které tvoří rozložení textu `@Body` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="caa3d-308">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="caa3d-309">`ButtonClass` je přiřazena hodnota `btn-success` v komponentě rozložení.</span><span class="sxs-lookup"><span data-stu-id="caa3d-309">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="caa3d-310">Všechny podřízené součásti mohou využívat tuto vlastnost prostřednictvím `ThemeInfo` šablony objektu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-310">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="caa3d-311">*Shared/CascadingValuesParametersLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-311">*Shared/CascadingValuesParametersLayout.cshtml*:</span></span>

```cshtml
@inherits BlazorLayoutComponent
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
    ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="caa3d-312">Chcete-li pomocí kaskádových hodnot, komponenty deklarovat kaskádové parametry s využitím `[CascadingParameter]` atribut nebo na základě hodnoty názvu řetězec:</span><span class="sxs-lookup"><span data-stu-id="caa3d-312">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")] PermInfo Permissions { get; set; }
```

<span data-ttu-id="caa3d-313">Vazba s hodnotou řetězce názvu platí, pokud máte více kaskádových hodnot stejného typu a pro jejich odlišení v rámci stejné podstrom.</span><span class="sxs-lookup"><span data-stu-id="caa3d-313">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="caa3d-314">Kaskádové hodnoty se váží k parametrům šablony podle typu.</span><span class="sxs-lookup"><span data-stu-id="caa3d-314">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="caa3d-315">V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta vytvoří vazbu `ThemeInfo` kaskádové hodnotu pro parametr šablony.</span><span class="sxs-lookup"><span data-stu-id="caa3d-315">In the sample app, the `CascadingValuesParametersTheme` component binds to the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="caa3d-316">Tento parametr se používá nastavení třídy šablony stylů CSS pro jedno z tlačítek zobrazí komponentou.</span><span class="sxs-lookup"><span data-stu-id="caa3d-316">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="caa3d-317">*Pages/CascadingValuesParametersTheme.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-317">*Pages/CascadingValuesParametersTheme.cshtml*:</span></span>

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
    int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a><span data-ttu-id="caa3d-318">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="caa3d-318">TabSet example</span></span>

<span data-ttu-id="caa3d-319">Parametry šablony také povolit součásti spolupracovat napříč hierarchií komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-319">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="caa3d-320">Představte si třeba následující *TabSet* příklad v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="caa3d-320">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="caa3d-321">Ukázková aplikace má `ITab` rozhraní, které karty implementace:</span><span class="sxs-lookup"><span data-stu-id="caa3d-321">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="caa3d-322">`CascadingValuesParametersTabSet` Komponenta používá `TabSet` komponenta, která obsahuje několik `Tab` komponenty:</span><span class="sxs-lookup"><span data-stu-id="caa3d-322">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

<span data-ttu-id="caa3d-323">Podřízené `Tab` součásti nejsou explicitně předány jako parametry `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-323">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="caa3d-324">Místo toho podřízené `Tab` komponenty jsou součástí podřízený obsah `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="caa3d-324">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="caa3d-325">Ale `TabSet` stále je potřeba vědět o každém `Tab` tak, aby ho může mít za následek záhlaví a na aktivní kartě. Povolit koordinace bez potřeby dalšího kódu, `TabSet` komponenty *samotný můžete zadat jako hodnotu kaskádové* , který pak převezme potomka `Tab` komponenty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-325">However, the `TabSet` still needs to know about each `Tab` so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="caa3d-326">*Components/TabSet.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-326">*Components/TabSet.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

<span data-ttu-id="caa3d-327">Potomka `Tab` součásti capture obsahující `TabSet` jako parametr šablony, proto `Tab` součásti přidat samy o sobě `TabSet` a souřadnice, na kterém `Tab` je aktivní.</span><span class="sxs-lookup"><span data-stu-id="caa3d-327">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which `Tab` is active.</span></span>

<span data-ttu-id="caa3d-328">*Components/Tab.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-328">*Components/Tab.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a><span data-ttu-id="caa3d-329">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="caa3d-329">Razor templates</span></span>

<span data-ttu-id="caa3d-330">Vykreslení fragmenty lze definovat pomocí syntaxe šablon Razor.</span><span class="sxs-lookup"><span data-stu-id="caa3d-330">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="caa3d-331">Šablony Razor představují způsob, jak definovat fragment kódu uživatelského rozhraní a předpokládají následující formát:</span><span class="sxs-lookup"><span data-stu-id="caa3d-331">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="caa3d-332">Následující příklad ukazuje, jak určit `RenderFragment` a `RenderFragment<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="caa3d-332">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="caa3d-333">*RazorTemplates.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="caa3d-333">*RazorTemplates.cshtml*:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="caa3d-334">Vykreslení fragmenty definované pomocí syntaxe Razor šablony mohou být předány jako argumenty bez vizuálního vzhledu součásti nebo vykresluje přímo.</span><span class="sxs-lookup"><span data-stu-id="caa3d-334">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="caa3d-335">Předchozí šablony jsou přímo vykreslí následujícím kódem Razor:</span><span class="sxs-lookup"><span data-stu-id="caa3d-335">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="caa3d-336">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="caa3d-336">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```
