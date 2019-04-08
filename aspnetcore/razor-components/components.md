---
title: Vytváření a používání komponent Razor
author: guardrex
description: Zjistěte, jak vytvořit a používat komponenty Razor, včetně jak vázat na data, zpracování událostí a spravovat životní cykly komponenty.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/components
ms.openlocfilehash: 00e07d496f4471f56d4184d1cb7c07c0715bea3f
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068356"
---
# <a name="create-and-use-razor-components"></a><span data-ttu-id="ecbde-103">Vytváření a používání komponent Razor</span><span class="sxs-lookup"><span data-stu-id="ecbde-103">Create and use Razor Components</span></span>

<span data-ttu-id="ecbde-104">Podle [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a [Morné Zaayman](https://github.com/MorneZaayman)</span><span class="sxs-lookup"><span data-stu-id="ecbde-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Morné Zaayman](https://github.com/MorneZaayman)</span></span>

<span data-ttu-id="ecbde-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ecbde-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="ecbde-106">Najdete v článku [Začínáme](xref:razor-components/get-started) tématu pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="ecbde-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="ecbde-107">Razor komponenty aplikace jsou sestaveny na základě *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="ecbde-107">Razor Components apps are built using *components*.</span></span> <span data-ttu-id="ecbde-108">Komponenta je samostatná blok uživatelského rozhraní (UI), například stránky, dialogové okno nebo formuláře.</span><span class="sxs-lookup"><span data-stu-id="ecbde-108">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="ecbde-109">Komponenta obsahuje kód HTML a zpracování logiku potřebnou k vložení dat nebo v reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ecbde-109">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="ecbde-110">Součásti jsou flexibilní a jednoduchý.</span><span class="sxs-lookup"><span data-stu-id="ecbde-110">Components are flexible and lightweight.</span></span> <span data-ttu-id="ecbde-111">Mohou být vnořené, znovu použít a sdílet mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-111">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="ecbde-112">Třídy součásti</span><span class="sxs-lookup"><span data-stu-id="ecbde-112">Component classes</span></span>

<span data-ttu-id="ecbde-113">Součásti jsou obvykle implementována v souborech Razor součásti (*.razor*) pomocí kombinace C# a značky HTML (*.cshtml* soubory se používají v aplikacích Blazor).</span><span class="sxs-lookup"><span data-stu-id="ecbde-113">Components are typically implemented in Razor Component files (*.razor*) using a combination of C# and HTML markup (*.cshtml* files are used in Blazor apps).</span></span>

<span data-ttu-id="ecbde-114">Dají se vytvářet komponenty ve Razor součásti aplikace pomocí *.cshtml* příponu souboru, tak dlouho, dokud soubory jsou označeny jako soubory součástí Razor pomocí `_RazorComponentInclude` vlastnosti Msbuildu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-114">Components can be authored in Razor Components apps using the *.cshtml* file extension as long as the files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="ecbde-115">Například aplikace vytvořená pomocí šablony Razor komponenty Určuje, že všechny *.cshtml* soubory pod *součásti* složky mají být považována za soubory součástí Razor:</span><span class="sxs-lookup"><span data-stu-id="ecbde-115">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components files:</span></span>

```xml
<_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="ecbde-116">Uživatelské rozhraní pro součást je definován v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="ecbde-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="ecbde-117">Dynamické vykreslování logiku (například smyčky, podmíněné příkazy, výrazy) přidána pomocí vložený C# syntaxe volá [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="ecbde-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="ecbde-118">Když je kompilován Razor součásti aplikace, bude značka jazyka HTML a C# logiku vykreslení se převedou na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-118">When a Razor Components app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="ecbde-119">Název generované třídy odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="ecbde-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="ecbde-120">Členy třídy komponenty jsou definovány v `@functions` blok (více než jeden `@functions` blok je povolený).</span><span class="sxs-lookup"><span data-stu-id="ecbde-120">Members of the component class are defined in a `@functions` block (more than one `@functions` block is permissible).</span></span> <span data-ttu-id="ecbde-121">V `@functions` bloku, stav komponent (vlastnosti, pole) zadán s parametrem metody pro zpracování událostí nebo definování dalších součástí logiky.</span><span class="sxs-lookup"><span data-stu-id="ecbde-121">In the `@functions` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span>

<span data-ttu-id="ecbde-122">Komponenta členy můžete poté použita jako tato součást je vykreslování pomocí logiky C# výrazy, které začínají `@`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-122">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="ecbde-123">Například C# pole se vykreslí vložením prefixu `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="ecbde-123">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="ecbde-124">Následující příklad vyhodnotí a vykreslí:</span><span class="sxs-lookup"><span data-stu-id="ecbde-124">The following example evaluates and renders:</span></span>

* `_headingFontStyle` <span data-ttu-id="ecbde-125">Hodnota vlastnosti šablon stylů CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-125">to the CSS property value for `font-style`.</span></span>
* `_headingText` <span data-ttu-id="ecbde-126">k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-126">to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="ecbde-127">Po se zpočátku zobrazí komponentu obnoví komponenty jeho vykreslení stromu v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="ecbde-127">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="ecbde-128">Součásti Razor poté porovnává větve vykreslení oproti předchozímu a platí všechny změny do prohlížeče Document Object Model (DOM).</span><span class="sxs-lookup"><span data-stu-id="ecbde-128">Razor Components then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="ecbde-129">Integrovat komponenty do aplikace Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="ecbde-129">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="ecbde-130">Komponenty pomocí stávající aplikace Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="ecbde-130">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="ecbde-131">Není nutné pro přepsání existujících stránek nebo zobrazení Razor komponent.</span><span class="sxs-lookup"><span data-stu-id="ecbde-131">There's no need to rewrite existing pages or views to use Razor Components.</span></span> <span data-ttu-id="ecbde-132">Při zobrazení stránky nebo zobrazení se komponenty jsou předkreslených&dagger; ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-132">When the page or view is rendered, components are prerendered&dagger; at the same time.</span></span> 

> [!NOTE]
> <span data-ttu-id="ecbde-133">&dagger;Dokončení fáze před vykreslením na straně serveru je ve výchozím nastavení povoleno pro Razor součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecbde-133">&dagger;Server-side prerendering is enabled for Razor Components apps by default.</span></span> <span data-ttu-id="ecbde-134">Aplikace na straně klienta Blazor bude podporovat dokončení fáze před vykreslením v nadcházející verzi Preview 4.</span><span class="sxs-lookup"><span data-stu-id="ecbde-134">Client-side Blazor apps will support prerendering in the upcoming Preview 4 release.</span></span> <span data-ttu-id="ecbde-135">Další informace najdete v tématu [aktualizace šablony/middlewaru, který má použít MapFallbackToPage/soubor](https://github.com/aspnet/AspNetCore/issues/8852).</span><span class="sxs-lookup"><span data-stu-id="ecbde-135">For more information, see [Update templates/middleware to use MapFallbackToPage/File](https://github.com/aspnet/AspNetCore/issues/8852).</span></span>

<span data-ttu-id="ecbde-136">K vykreslení komponenty z stránku nebo zobrazení, použijte `RenderComponentAsync<TComponent>` metodu helper HTML:</span><span class="sxs-lookup"><span data-stu-id="ecbde-136">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="ecbde-137">Zatím nejsou interaktivní ve verzi Preview 3 součástí vykreslí ze stránky a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ecbde-137">Components rendered from pages and views aren't yet interactive in the Preview 3 release.</span></span> <span data-ttu-id="ecbde-138">Například výběrem tlačítka neaktivuje volání metody.</span><span class="sxs-lookup"><span data-stu-id="ecbde-138">For example, selecting a button doesn't trigger a method call.</span></span> <span data-ttu-id="ecbde-139">Budoucí verze preview se adresy toto omezení a přidat podporu pro vykreslení součásti pomocí běžné syntaxe prvků a atributů.</span><span class="sxs-lookup"><span data-stu-id="ecbde-139">A future preview will address this limitation and add support for rendering components using the normal element and attribute syntax.</span></span>

<span data-ttu-id="ecbde-140">Při zobrazení stránky a můžou používat komponenty, neplatí první.</span><span class="sxs-lookup"><span data-stu-id="ecbde-140">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="ecbde-141">Součásti nelze použít zobrazení a stránky konkrétní scénáře, jako je částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="ecbde-141">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="ecbde-142">Chcete-li použít logiku z částečného zobrazení v komponentě, faktor si logiky částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-142">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

## <a name="using-components"></a><span data-ttu-id="ecbde-143">Pomocí komponent</span><span class="sxs-lookup"><span data-stu-id="ecbde-143">Using components</span></span>

<span data-ttu-id="ecbde-144">Součásti můžete zahrnout další součásti je deklarací pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="ecbde-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="ecbde-145">Kód pro použití komponenty vypadá jako značku jazyka HTML, kde název značky je typ součásti.</span><span class="sxs-lookup"><span data-stu-id="ecbde-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="ecbde-146">Následující kód vykreslí `HeadingComponent` (*HeadingComponent.cshtml*) instance:</span><span class="sxs-lookup"><span data-stu-id="ecbde-146">The following markup renders a `HeadingComponent` (*HeadingComponent.cshtml*) instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a><span data-ttu-id="ecbde-147">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="ecbde-147">Component parameters</span></span>

<span data-ttu-id="ecbde-148">Může mít komponenty *parametry komponenty*, které jsou definovány pomocí *neveřejné* vlastnosti komponentní třída upravené pomocí `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-148">Components can have *component parameters*, which are defined using *non-public* properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="ecbde-149">Atributy můžete zadat argumenty pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-149">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="ecbde-150">V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` vlastnost `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="ecbde-150">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`:</span></span>

<span data-ttu-id="ecbde-151">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-151">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

<span data-ttu-id="ecbde-152">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-152">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a><span data-ttu-id="ecbde-153">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="ecbde-153">Child content</span></span>

<span data-ttu-id="ecbde-154">Součásti můžete nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="ecbde-154">Components can set the content of another component.</span></span> <span data-ttu-id="ecbde-155">Přiřazení součásti najdete zde obsah mezi značky, které určují přijímající komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-155">The assigning component provides the content between the tags that specify the receiving component.</span></span> <span data-ttu-id="ecbde-156">Například `ParentComponent` můžete zadat obsah pro vykreslování podle podřízené součásti tak, že je obsah uvnitř `<ChildComponent>` značky.</span><span class="sxs-lookup"><span data-stu-id="ecbde-156">For example, a `ParentComponent` can provide content for rendering by a Child component by placing the content inside `<ChildComponent>` tags.</span></span>

<span data-ttu-id="ecbde-157">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-157">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

<span data-ttu-id="ecbde-158">Obsahuje podřízené součásti `ChildContent` vlastnost, která představuje `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-158">The Child component has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="ecbde-159">Hodnota `ChildContent` je umístěn ve značkách podřízené součásti, kde má být vykreslen obsah.</span><span class="sxs-lookup"><span data-stu-id="ecbde-159">The value of `ChildContent` is positioned in the child component's markup where the content should be rendered.</span></span> <span data-ttu-id="ecbde-160">V následujícím příkladu, hodnota `ChildContent` přijme od nadřazené komponenty a vykreslit v rámci panelu Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-160">In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="ecbde-161">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-161">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> <span data-ttu-id="ecbde-162">Vlastnost příjmu `RenderFragment` obsahu musí mít název `ChildContent` konvencí.</span><span class="sxs-lookup"><span data-stu-id="ecbde-162">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

## <a name="data-binding"></a><span data-ttu-id="ecbde-163">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="ecbde-163">Data binding</span></span>

<span data-ttu-id="ecbde-164">Vazba dat na komponent a prvky modelu DOM se dosahuje pomocí `bind` atribut.</span><span class="sxs-lookup"><span data-stu-id="ecbde-164">Data binding to both components and DOM elements is accomplished with the `bind` attribute.</span></span> <span data-ttu-id="ecbde-165">Následující příklad vytvoří vazbu `ItalicsCheck` vlastnost na zaškrtávací políčko zaškrtnuto, stav:</span><span class="sxs-lookup"><span data-stu-id="ecbde-165">The following example binds the `ItalicsCheck` property to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck" />
```

<span data-ttu-id="ecbde-166">Při zaškrtnutí políčka je a zrušte zaškrtnutí, hodnota vlastnosti je aktualizována na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="ecbde-166">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="ecbde-167">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní, pouze v případě, že součást je vykresleno, ne v reakci na měnící se hodnota vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="ecbde-167">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="ecbde-168">Protože komponenty vykreslování sami po spuštění kódu obslužné rutiny události, aktualizace vlastností se obvykle projeví v uživatelském rozhraní ihned.</span><span class="sxs-lookup"><span data-stu-id="ecbde-168">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="ecbde-169">Pomocí `bind` s `CurrentValue` vlastnosti (`<input bind="@CurrentValue" />`) je v podstatě ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="ecbde-169">Using `bind` with a `CurrentValue` property (`<input bind="@CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="ecbde-170">Při vykreslování komponentu `value` elementu input pochází z `CurrentValue` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ecbde-170">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="ecbde-171">Když uživatel zadá v textovém poli `onchange` událost se aktivuje a `CurrentValue` je nastavena na hodnotu změněné.</span><span class="sxs-lookup"><span data-stu-id="ecbde-171">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="ecbde-172">Ve skutečnosti je generování kódu poněkud složitější, protože `bind` zpracovává několik případů, kdy jsou provedeny převody typu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-172">In reality, the code generation is a little more complex because `bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="ecbde-173">V zásadě `bind` přidruží aktuální hodnotu výrazu s `value` obslužné rutiny a atributu změny pomocí zaregistrovaná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="ecbde-173">In principle, `bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="ecbde-174">Kromě `onchange`, vlastnost může být vázána pomocí jiné události, jako jsou `oninput` tím, že je explicitní více o tom, co k vytvoření vazby:</span><span class="sxs-lookup"><span data-stu-id="ecbde-174">In addition to `onchange`, the property can be bound using other events like `oninput` by being more explicit about what to bind to:</span></span>

```cshtml
<input type="text" bind-value-oninput="@CurrentValue" />
```

<span data-ttu-id="ecbde-175">Na rozdíl od `onchange`, `oninput` aktivována pro každý znak, který je vstup do textového pole.</span><span class="sxs-lookup"><span data-stu-id="ecbde-175">Unlike `onchange`, `oninput` fires for every character that is input into the text box.</span></span>

**<span data-ttu-id="ecbde-176">Formátovací řetězce</span><span class="sxs-lookup"><span data-stu-id="ecbde-176">Format strings</span></span>**

<span data-ttu-id="ecbde-177">Vytváření datových vazeb funguje s <xref:System.DateTime> řetězce formátu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-177">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="ecbde-178">V tuto chvíli nejsou k dispozici jiných výrazech formátu, například měny nebo číselných formátů.</span><span class="sxs-lookup"><span data-stu-id="ecbde-178">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd" />

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="ecbde-179">`format-value` Atribut specifikuje formát data použít `value` z `input` elementu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-179">The `format-value` attribute specifies the date format to apply to the `value` of the `input` element.</span></span> <span data-ttu-id="ecbde-180">Formát slouží také k analýze hodnotu při `onchange` dojde k události.</span><span class="sxs-lookup"><span data-stu-id="ecbde-180">The format is also used to parse the value when an `onchange` event occurs.</span></span>

**<span data-ttu-id="ecbde-181">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="ecbde-181">Component parameters</span></span>**

<span data-ttu-id="ecbde-182">Vazba také rozpozná parametry komponenty, kde `bind-{property}` mohl vytvořit vazbu vlastnosti komponentami.</span><span class="sxs-lookup"><span data-stu-id="ecbde-182">Binding also recognizes component parameters, where `bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="ecbde-183">Používá následující komponenty `ChildComponent` a vytvoří vazbu `ParentYear` parametr z nadřazeného `Year` parametru u podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="ecbde-183">The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

<span data-ttu-id="ecbde-184">Nadřazené komponenty:</span><span class="sxs-lookup"><span data-stu-id="ecbde-184">Parent component:</span></span>

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

    void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="ecbde-185">Podřízené součásti:</span><span class="sxs-lookup"><span data-stu-id="ecbde-185">Child component:</span></span>

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

<span data-ttu-id="ecbde-186">Načítají `ParentComponent` vytváří následující značky:</span><span class="sxs-lookup"><span data-stu-id="ecbde-186">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="ecbde-187">Pokud hodnota `ParentYear` vlastnost se změní tak, že vyberete tlačítko v `ParentComponent`, `Year` vlastnost `ChildComponent` se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="ecbde-187">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="ecbde-188">Nová hodnota `Year` se vykreslí v uživatelském rozhraní při `ParentComponent` je rerendered:</span><span class="sxs-lookup"><span data-stu-id="ecbde-188">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="ecbde-189">`Year` Parametr je s možností vazby, protože má doprovodná `YearChanged` událost, která odpovídá typu používaného `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="ecbde-189">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="ecbde-190">Podle konvence `<ChildComponent bind-Year="@ParentYear" />` je v podstatě ekvivalentní zápisu</span><span class="sxs-lookup"><span data-stu-id="ecbde-190">By convention, `<ChildComponent bind-Year="@ParentYear" />` is essentially equivalent to writing,</span></span>

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

<span data-ttu-id="ecbde-191">Obecně platí, vlastnost může být vázána na odpovídající obslužná rutina události pomocí `bind-property-event` atribut.</span><span class="sxs-lookup"><span data-stu-id="ecbde-191">In general, a property can be bound to a corresponding event handler using `bind-property-event` attribute.</span></span>

## <a name="event-handling"></a><span data-ttu-id="ecbde-192">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="ecbde-192">Event handling</span></span>

<span data-ttu-id="ecbde-193">Součásti syntaxe Razor poskytují funkce zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="ecbde-193">Razor Components provide event handling features.</span></span> <span data-ttu-id="ecbde-194">Atribut HTML elementu s názvem `on<event>` (například `onclick`, `onsubmit`) s hodnotou typu delegáta Razor součásti považuje za hodnotu atributu obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="ecbde-194">For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Razor Components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="ecbde-195">Název atributu vždy začíná `on`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-195">The attribute's name always starts with `on`.</span></span>

<span data-ttu-id="ecbde-196">Následující kód volá `UpdateHeading` metodu po výběru tlačítka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ecbde-196">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="ecbde-197">Následující kód volá `CheckboxChanged` metoda při změně zaškrtávacího políčka v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ecbde-197">The following code calls the `CheckboxChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged" />

@functions {
    void CheckboxChanged()
    {
        ...
    }
}
```

<span data-ttu-id="ecbde-198">Obslužné rutiny událostí může být také asynchronní a zpět <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="ecbde-198">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="ecbde-199">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-199">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="ecbde-200">Výjimky se protokolují, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="ecbde-200">Exceptions are logged when they occur.</span></span>

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

<span data-ttu-id="ecbde-201">Pro některé události nejsou povoleny typy argumentů události specifické pro události.</span><span class="sxs-lookup"><span data-stu-id="ecbde-201">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="ecbde-202">Pokud přístup k jednomu z těchto typů událostí není nezbytné, není potřeba ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="ecbde-202">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="ecbde-203">Seznam podporovaných událostí argumentů je:</span><span class="sxs-lookup"><span data-stu-id="ecbde-203">The list of supported event arguments is:</span></span>

* <span data-ttu-id="ecbde-204">UIEventArgs</span><span class="sxs-lookup"><span data-stu-id="ecbde-204">UIEventArgs</span></span>
* <span data-ttu-id="ecbde-205">UIChangeEventArgs</span><span class="sxs-lookup"><span data-stu-id="ecbde-205">UIChangeEventArgs</span></span>
* <span data-ttu-id="ecbde-206">UIKeyboardEventArgs</span><span class="sxs-lookup"><span data-stu-id="ecbde-206">UIKeyboardEventArgs</span></span>
* <span data-ttu-id="ecbde-207">UIMouseEventArgs</span><span class="sxs-lookup"><span data-stu-id="ecbde-207">UIMouseEventArgs</span></span>

<span data-ttu-id="ecbde-208">Výrazy lambda lze také použít:</span><span class="sxs-lookup"><span data-stu-id="ecbde-208">Lambda expressions can also be used:</span></span>

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="ecbde-209">Často je vhodné zavřít další hodnoty, například během iterace přes sadu elementů.</span><span class="sxs-lookup"><span data-stu-id="ecbde-209">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="ecbde-210">Následující příklad vytvoří tři tlačítka, každý z který volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a tlačítko (`buttonNumber`) při výběru v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ecbde-210">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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

> [!NOTE]
> <span data-ttu-id="ecbde-211">Proveďte **není** použít proměnnou smyčky (`i`) v `for` smyčky přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="ecbde-211">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="ecbde-212">V opačném případě se používá stejnou proměnnou všechny výrazy lambda způsobí `i`hodnotu být stejné ve všech výrazů lambda.</span><span class="sxs-lookup"><span data-stu-id="ecbde-212">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="ecbde-213">Zachytit její hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak přes ni.</span><span class="sxs-lookup"><span data-stu-id="ecbde-213">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="ecbde-214">Zachycení odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="ecbde-214">Capture references to components</span></span>

<span data-ttu-id="ecbde-215">Komponenta odkazy poskytují způsob, jak získat odkaz na instanci komponenty tak, aby příkazy do této instance, můžete vydat, jako `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-215">Component references provide a way get a reference to a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="ecbde-216">Chcete-li zachytit odkazem komponenty, přidejte `ref` atribut podřízené součásti a pak definovat pole se stejným názvem a stejného typu jako podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="ecbde-216">To capture a component reference, add a `ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

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

<span data-ttu-id="ecbde-217">Při vykreslování komponentu `loginDialog` pole se vyplní `MyLoginDialog` podřízené instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-217">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="ecbde-218">Potom můžete vyvolat metody rozhraní .NET na instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-218">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ecbde-219">`loginDialog` Proměnná je vyplněný pouze poté, co se vykreslí komponentu a zahrnuje její výstup `MyLoginDialog` elementu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-219">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="ecbde-220">Až do bodu není nutné nic odkazovat.</span><span class="sxs-lookup"><span data-stu-id="ecbde-220">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="ecbde-221">K manipulaci s odkazy na součásti po dokončení vykreslení komponentu, použijte `OnAfterRenderAsync` nebo `OnAfterRender` metody.</span><span class="sxs-lookup"><span data-stu-id="ecbde-221">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="ecbde-222">Při zachytávání odkazů na komponenty používá podobné syntaxi k [zachytávání odkazy na prvky](xref:razor-components/javascript-interop#capture-references-to-elements), není [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop) funkce.</span><span class="sxs-lookup"><span data-stu-id="ecbde-222">While capturing component references uses a similar syntax to [capturing element references](xref:razor-components/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:razor-components/javascript-interop) feature.</span></span> <span data-ttu-id="ecbde-223">Nejsou součástí odkazy předané do kódu jazyka JavaScript; se používá pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="ecbde-223">Component references aren't passed to JavaScript code; they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="ecbde-224">Proveďte **není** mutovat stavu podřízenými komponentami pomocí odkazů na komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-224">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="ecbde-225">Místo toho použijte normální deklarované parametry k předání dat podřízenými komponentami.</span><span class="sxs-lookup"><span data-stu-id="ecbde-225">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="ecbde-226">To způsobí, že podřízené součásti rerender ve správném čase automaticky.</span><span class="sxs-lookup"><span data-stu-id="ecbde-226">This causes child components to rerender at the correct times automatically.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="ecbde-227">Životní cyklus metody</span><span class="sxs-lookup"><span data-stu-id="ecbde-227">Lifecycle methods</span></span>

`OnInitAsync` <span data-ttu-id="ecbde-228">a `OnInit` spouštění kódu se inicializovat komponentu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-228">and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="ecbde-229">Chcete-li provádění asynchronní operace, použijte `OnInitAsync` a `await` – klíčové slovo v operaci:</span><span class="sxs-lookup"><span data-stu-id="ecbde-229">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="ecbde-230">Synchronní operace, použijte `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="ecbde-230">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` <span data-ttu-id="ecbde-231">a `OnParametersSet` se volá, když součást přijme parametry ze svého nadřazeného objektu a hodnoty jsou přiřazeny k vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="ecbde-231">and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="ecbde-232">Tyto metody jsou provedeny po inicializaci součásti a poté je vykreslen komponentu pokaždé, když:</span><span class="sxs-lookup"><span data-stu-id="ecbde-232">These methods are executed after component initialization and then each time the component is rendered:</span></span>

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

`OnAfterRenderAsync` <span data-ttu-id="ecbde-233">a `OnAfterRender` se volá se po vykreslení komponentu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-233">and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="ecbde-234">V tomto okamžiku se vyplní elementu a součást odkazy.</span><span class="sxs-lookup"><span data-stu-id="ecbde-234">Element and component references are populated at this point.</span></span> <span data-ttu-id="ecbde-235">Pomocí této fázi můžete provést další inicializaci postup pomocí vykreslovaný obsah, jako je aktivace JavaScript knihovny třetích stran, které pracují s vykreslené elementy modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="ecbde-235">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

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

`SetParameters` <span data-ttu-id="ecbde-236">může být potlačena za účelem spouštění kódu předtím, než jsou nastavené parametry:</span><span class="sxs-lookup"><span data-stu-id="ecbde-236">can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="ecbde-237">Pokud `base.SetParameters` není vyvolána, můžete vlastní kód interpretaci příchozí hodnoty parametrů v jakékoli požadované stejně, jako.</span><span class="sxs-lookup"><span data-stu-id="ecbde-237">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="ecbde-238">Například příchozí parametry nejsou potřeba přiřadit k vlastnosti ve třídě.</span><span class="sxs-lookup"><span data-stu-id="ecbde-238">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

`ShouldRender` <span data-ttu-id="ecbde-239">může být potlačena za účelem potlačit aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ecbde-239">can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="ecbde-240">Implementace vrátí-li `true`, aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ecbde-240">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="ecbde-241">I když `ShouldRender` je přepsána, komponenta je vždy Počáteční vykreslení.</span><span class="sxs-lookup"><span data-stu-id="ecbde-241">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="ecbde-242">Vyřazení komponenty pomocí rozhraní IDisposable</span><span class="sxs-lookup"><span data-stu-id="ecbde-242">Component disposal with IDisposable</span></span>

<span data-ttu-id="ecbde-243">Pokud komponenta implementuje <xref:System.IDisposable>, [metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) se volá, když je součást z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ecbde-243">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="ecbde-244">Používá následující komponenty `@implements IDisposable` a `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="ecbde-244">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="ecbde-245">Směrování</span><span class="sxs-lookup"><span data-stu-id="ecbde-245">Routing</span></span>

<span data-ttu-id="ecbde-246">Směrování v součástech Razor se dosahuje tím, že poskytuje šablona trasy pro jednotlivé dostupné komponenty v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ecbde-246">Routing in Razor Components is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="ecbde-247">Při *.cshtml* soubor s `@page` – direktiva je zkompilován, dostane generované třídy <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadání šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="ecbde-247">When a *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="ecbde-248">Za běhu, směrovač hledá komponentní třídy s `RouteAttribute` a vykreslí podle toho, která komponenta má šablona trasy, která odpovídá požadovanou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="ecbde-248">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="ecbde-249">Více šablon trasy můžete použít pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-249">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="ecbde-250">Následující komponenty jsou reaguje na požadavky pro `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="ecbde-250">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="ecbde-251">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="ecbde-251">Route parameters</span></span>

<span data-ttu-id="ecbde-252">Součásti mohou přijímat parametry trasy z šablonu trasy, které jsou součástí `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="ecbde-252">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="ecbde-253">Směrovač používá parametry trasy k naplnění odpovídající komponenta parametry.</span><span class="sxs-lookup"><span data-stu-id="ecbde-253">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="ecbde-254">*RouteParameter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-254">*RouteParameter.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

<span data-ttu-id="ecbde-255">Volitelné parametry nejsou podporovány, tedy dvě `@page` direktivy se použijí v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-255">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="ecbde-256">První umožňuje přechod na komponenty bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="ecbde-256">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="ecbde-257">Druhá `@page` trvá – direktiva `{text}` parametr trasa a přiřadí hodnotu do proměnné `Text` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ecbde-257">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="ecbde-258">Základní třída dědičnosti "použití modelu code-behind" prostředí</span><span class="sxs-lookup"><span data-stu-id="ecbde-258">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="ecbde-259">Soubory komponent (*.cshtml*) kombinovat kód HTML a C# zpracování kódu ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="ecbde-259">Component files (*.cshtml*) mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="ecbde-260">`@inherits` – Direktiva je možné poskytovat prostředí "použití modelu code-behind", který odděluje komponenty značek z zpracování kódu Razor součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecbde-260">The `@inherits` directive can be used to provide Razor Components apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="ecbde-261">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ukazuje, jak komponenty může dědit základní třídy `BlazorRocksBase`, aby vznikl komponenty vlastnosti a metody.</span><span class="sxs-lookup"><span data-stu-id="ecbde-261">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="ecbde-262">*BlazorRocks.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-262">*BlazorRocks.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

<span data-ttu-id="ecbde-263">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-263">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="ecbde-264">Základní třída musí být odvozený z: `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-264">The base class should derive from `ComponentBase`.</span></span>

## <a name="razor-support"></a><span data-ttu-id="ecbde-265">Podpora Razor</span><span class="sxs-lookup"><span data-stu-id="ecbde-265">Razor support</span></span>

**<span data-ttu-id="ecbde-266">Direktivy Razor</span><span class="sxs-lookup"><span data-stu-id="ecbde-266">Razor directives</span></span>**

<span data-ttu-id="ecbde-267">V následující tabulce jsou uvedeny direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="ecbde-267">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="ecbde-268">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="ecbde-268">Directive</span></span> | <span data-ttu-id="ecbde-269">Popis</span><span class="sxs-lookup"><span data-stu-id="ecbde-269">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="ecbde-270">\@Funkce</span><span class="sxs-lookup"><span data-stu-id="ecbde-270">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="ecbde-271">Přidá C# blok kódu na komponentu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-271">Adds a C# code block to a component.</span></span> |
| `@implements` | <span data-ttu-id="ecbde-272">Implementuje rozhraní pro třídu vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-272">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="ecbde-273">\@Dědí</span><span class="sxs-lookup"><span data-stu-id="ecbde-273">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="ecbde-274">Poskytuje plnou kontrolu nad třídu, která dědí komponentu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-274">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="ecbde-275">\@Vložení</span><span class="sxs-lookup"><span data-stu-id="ecbde-275">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="ecbde-276">Vkládání ze služby umožňuje [kontejneru služby](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ecbde-276">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ecbde-277">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ecbde-277">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="ecbde-278">Určuje komponentu rozložení.</span><span class="sxs-lookup"><span data-stu-id="ecbde-278">Specifies a layout component.</span></span> <span data-ttu-id="ecbde-279">Rozložení komponenty umožňují zabránit zdvojení kódu a nekonzistence.</span><span class="sxs-lookup"><span data-stu-id="ecbde-279">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="ecbde-280">\@Stránka</span><span class="sxs-lookup"><span data-stu-id="ecbde-280">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="ecbde-281">Určuje, že by měla komponenta zpracování požadavků přímo.</span><span class="sxs-lookup"><span data-stu-id="ecbde-281">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="ecbde-282">`@page` – Direktiva je možné zadat při trasy a volitelné parametry.</span><span class="sxs-lookup"><span data-stu-id="ecbde-282">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="ecbde-283">Na rozdíl od Razor Pages `@page` – direktiva nemusí být první – direktiva v horní části souboru.</span><span class="sxs-lookup"><span data-stu-id="ecbde-283">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="ecbde-284">Další informace najdete v tématu [směrování](xref:razor-components/routing).</span><span class="sxs-lookup"><span data-stu-id="ecbde-284">For more information, see [Routing](xref:razor-components/routing).</span></span> |
| [<span data-ttu-id="ecbde-285">\@použití</span><span class="sxs-lookup"><span data-stu-id="ecbde-285">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="ecbde-286">Přidá C# `using` směrnice do třídy vygenerované komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-286">Adds the C# `using` directive to the generated component class.</span></span> |
| [<span data-ttu-id="ecbde-287">\@addTagHelper</span><span class="sxs-lookup"><span data-stu-id="ecbde-287">\@addTagHelper</span></span>](xref:mvc/views/razor#tag-helpers) | <span data-ttu-id="ecbde-288">Použít `@addTagHelper` použít komponentu v jiném sestavení než sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ecbde-288">Use `@addTagHelper` to use a component in a different assembly than the app's assembly.</span></span> |

**<span data-ttu-id="ecbde-289">Podmíněné atributy</span><span class="sxs-lookup"><span data-stu-id="ecbde-289">Conditional attributes</span></span>**

<span data-ttu-id="ecbde-290">Atributy jsou vykreslovány podmíněně na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="ecbde-290">Attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="ecbde-291">Pokud je hodnota `false` nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="ecbde-291">If the value is `false` or `null`,  the attribute isn't rendered.</span></span> <span data-ttu-id="ecbde-292">Pokud je hodnota `true`, atribut je vykreslen minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="ecbde-292">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="ecbde-293">V následujícím příkladu `IsCompleted` Určuje, zda `checked` se vykreslí v značky ovládacího prvku:</span><span class="sxs-lookup"><span data-stu-id="ecbde-293">In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="ecbde-294">Pokud `IsCompleted` je `true`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="ecbde-294">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="ecbde-295">Pokud `IsCompleted` je `false`, zaškrtněte políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="ecbde-295">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

**<span data-ttu-id="ecbde-296">Další informace o syntaxi Razor</span><span class="sxs-lookup"><span data-stu-id="ecbde-296">Additional information on Razor</span></span>**

<span data-ttu-id="ecbde-297">Další informace o syntaxi Razor, najdete v článku [referenční příručka syntaxe Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="ecbde-297">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="ecbde-298">Raw HTML</span><span class="sxs-lookup"><span data-stu-id="ecbde-298">Raw HTML</span></span>

<span data-ttu-id="ecbde-299">Řetězce jsou obvykle vykreslen pomocí modelu DOM textové uzly, což znamená, že všechny značky, které mohou obsahovat je ignorována a považována jako prostý text.</span><span class="sxs-lookup"><span data-stu-id="ecbde-299">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="ecbde-300">Pokud chcete zobrazit nezpracovaný kód HTML, zabalit obsah HTML v `MarkupString` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-300">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="ecbde-301">Hodnota je analyzovat ve formátu HTML nebo SVG a vložit do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="ecbde-301">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="ecbde-302">Vykreslování nezpracovaný kód HTML vytvořený z libovolného nedůvěryhodný zdroj je **bezpečnostní riziko** a mělo by se vyhnout!</span><span class="sxs-lookup"><span data-stu-id="ecbde-302">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="ecbde-303">Následující příklad ukazuje použití `MarkupString` typu přidáte blok statického obsahu HTML vykresleného výstupu součásti:</span><span class="sxs-lookup"><span data-stu-id="ecbde-303">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@functions {
    string myMarkup = "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="ecbde-304">Bez vizuálního vzhledu součásti</span><span class="sxs-lookup"><span data-stu-id="ecbde-304">Templated components</span></span>

<span data-ttu-id="ecbde-305">Bez vizuálního vzhledu komponenty jsou součástí, které přijímají jeden nebo více šablon uživatelského rozhraní jako parametry, které lze použít jako součást logiky komponenty vykreslování.</span><span class="sxs-lookup"><span data-stu-id="ecbde-305">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="ecbde-306">Bez vizuálního vzhledu komponenty umožňují vytvářet vyšší úrovně součásti, které jsou více než regulární komponenty opakovaně použitelné.</span><span class="sxs-lookup"><span data-stu-id="ecbde-306">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="ecbde-307">Zahrnují několik příkladů:</span><span class="sxs-lookup"><span data-stu-id="ecbde-307">A couple of examples include:</span></span>

* <span data-ttu-id="ecbde-308">Komponenta tabulky, která umožňuje uživateli zadat šablony pro záhlaví tabulky, řádky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="ecbde-308">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="ecbde-309">Seznam součástí, která umožňuje uživateli zadat šablonu pro vykreslování položky v seznamu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-309">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="ecbde-310">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="ecbde-310">Template parameters</span></span>

<span data-ttu-id="ecbde-311">Bez vizuálního vzhledu součásti je definován tak, že zadáte jeden nebo více parametrů součást typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="ecbde-311">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="ecbde-312">Vykreslení fragment reprezentuje segment, který uživatelského rozhraní, které je vykresleno komponentou.</span><span class="sxs-lookup"><span data-stu-id="ecbde-312">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="ecbde-313">Vykreslení fragment volitelně přebírá parametr, který lze zadat, pokud je vyvolána fragment vykreslení.</span><span class="sxs-lookup"><span data-stu-id="ecbde-313">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="ecbde-314">*Components/TableTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-314">*Components/TableTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

<span data-ttu-id="ecbde-315">Při použití komponenty bez vizuálního vzhledu, parametry šablony lze pomocí podřízené prvky, které odpovídají názvům parametry (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="ecbde-315">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="ecbde-316">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="ecbde-316">Template context parameters</span></span>

<span data-ttu-id="ecbde-317">Komponenta argumenty typu `RenderFragment<T>` předaný jako elementy mají implicitní parametr s názvem `context` (například z předchozí příklad kódu `@context.PetId`), ale můžete změnit pomocí parametru název `Context` atribut na podřízené element.</span><span class="sxs-lookup"><span data-stu-id="ecbde-317">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="ecbde-318">V následujícím příkladu `RowTemplate` elementu `Context` Určuje atribut `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="ecbde-318">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="ecbde-319">Alternativně můžete zadat `Context` atribut na prvek součásti.</span><span class="sxs-lookup"><span data-stu-id="ecbde-319">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="ecbde-320">Zadaný `Context` atributu platí pro všechny parametry určené šablony.</span><span class="sxs-lookup"><span data-stu-id="ecbde-320">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="ecbde-321">To může být užitečné, pokud chcete zadat název obsahu parametru pro implicitní podřízený obsah (bez jakékoli zabalení podřízený element).</span><span class="sxs-lookup"><span data-stu-id="ecbde-321">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="ecbde-322">V následujícím příkladu `Context` atributu se zobrazí na `TableTemplate` elementu a platí pro všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="ecbde-322">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="ecbde-323">Obecné typy komponenty</span><span class="sxs-lookup"><span data-stu-id="ecbde-323">Generic-typed components</span></span>

<span data-ttu-id="ecbde-324">Bez vizuálního vzhledu součásti jsou často obecně typu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-324">Templated components are often generically typed.</span></span> <span data-ttu-id="ecbde-325">Například komponentu obecného seznamu zobrazit šablonu můžete použít k vykreslení `IEnumerable<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-325">For example, a generic List View Template component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="ecbde-326">K definování obecné součásti, použijte `@typeparam` směrnice a určete parametry typu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-326">To define a generic component, use the `@typeparam` directive to specify type parameters.</span></span>

<span data-ttu-id="ecbde-327">*Components/ListViewTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-327">*Components/ListViewTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml)]

<span data-ttu-id="ecbde-328">Při použití komponenty obecného typu, parametr typu je odvozený Pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="ecbde-328">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="ecbde-329">V opačném případě parametr typu musí být explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-329">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="ecbde-330">V následujícím příkladu `TItem="Pet"` Určuje typ:</span><span class="sxs-lookup"><span data-stu-id="ecbde-330">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="ecbde-331">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="ecbde-331">Cascading values and parameters</span></span>

<span data-ttu-id="ecbde-332">V některých případech je nevhodné toku dat z jako součást nadřazené komponenty potomka pomocí [parametry komponenty](#component-parameters), zvlášť pokud máte několik vrstev komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-332">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="ecbde-333">Kaskádové hodnoty a parametry tento problém vyřešit tím, že poskytuje pohodlný způsob pro komponentu předchůdce k zadání hodnoty pro všechny jeho podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="ecbde-333">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="ecbde-334">Kaskádové hodnoty a parametry také poskytnout přístup pro součásti pro koordinaci.</span><span class="sxs-lookup"><span data-stu-id="ecbde-334">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="ecbde-335">Příklad motiv</span><span class="sxs-lookup"><span data-stu-id="ecbde-335">Theme example</span></span>

<span data-ttu-id="ecbde-336">V následujícím *motiv* příklad z ukázkové aplikace `ThemeInfo` třída určuje informace o motivech tok součástí hierarchií směrem dolů, aby všechna tlačítka v rámci dané části aplikace sdílet stejný styl.</span><span class="sxs-lookup"><span data-stu-id="ecbde-336">In the following *Theme* example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="ecbde-337">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-337">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="ecbde-338">Jako součást předchůdce může poskytnout kaskádové hodnotu pomocí komponenty kaskádové hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-338">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="ecbde-339">Součást CSS hodnoty zabalí podstrom součástí hierarchie a poskytuje jednu hodnotu pro všechny komponenty v rámci této podstrom.</span><span class="sxs-lookup"><span data-stu-id="ecbde-339">The Cascading Value component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="ecbde-340">Například ukázkové aplikace určuje informace o motivech (`ThemeInfo`) v jednom z rozložení aplikace jako parametr šablony pro všechny součásti, které tvoří rozložení textu `@Body` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ecbde-340">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> `ButtonClass` <span data-ttu-id="ecbde-341">je přiřazena hodnota `btn-success` v komponentě rozložení.</span><span class="sxs-lookup"><span data-stu-id="ecbde-341">is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="ecbde-342">Všechny podřízené součásti mohou využívat tuto vlastnost prostřednictvím `ThemeInfo` šablony objektu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-342">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="ecbde-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span></span>

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
    ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="ecbde-344">Chcete-li pomocí kaskádových hodnot, komponenty deklarovat kaskádové parametry s využitím `[CascadingParameter]` atribut nebo na základě hodnoty názvu řetězec:</span><span class="sxs-lookup"><span data-stu-id="ecbde-344">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")] PermInfo Permissions { get; set; }
```

<span data-ttu-id="ecbde-345">Vazba s hodnotou řetězce názvu platí, pokud máte více kaskádových hodnot stejného typu a pro jejich odlišení v rámci stejné podstrom.</span><span class="sxs-lookup"><span data-stu-id="ecbde-345">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="ecbde-346">Kaskádové hodnoty se váží k parametrům šablony podle typu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-346">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="ecbde-347">V ukázkové aplikaci, komponentě CSS motiv hodnoty parametrů vytvoří vazbu `ThemeInfo` kaskádové hodnotu pro parametr šablony.</span><span class="sxs-lookup"><span data-stu-id="ecbde-347">In the sample app, the Cascading Values Parameters Theme component binds to the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="ecbde-348">Tento parametr se používá nastavení třídy šablony stylů CSS pro jedno z tlačítek zobrazí komponentou.</span><span class="sxs-lookup"><span data-stu-id="ecbde-348">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="ecbde-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span></span>

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

### <a name="tabset-example"></a><span data-ttu-id="ecbde-350">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="ecbde-350">TabSet example</span></span>

<span data-ttu-id="ecbde-351">Parametry šablony také povolit součásti spolupracovat napříč hierarchií komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-351">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="ecbde-352">Představte si třeba následující *TabSet* příklad v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ecbde-352">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="ecbde-353">Ukázková aplikace má `ITab` rozhraní, které karty implementace:</span><span class="sxs-lookup"><span data-stu-id="ecbde-353">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="ecbde-354">Komponenta CSS hodnoty parametrů TabSet používá součásti záložku, která obsahuje několik komponent kartu:</span><span class="sxs-lookup"><span data-stu-id="ecbde-354">The Cascading Values Parameters TabSet component uses the Tab Set component, which contains several Tab components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

<span data-ttu-id="ecbde-355">Podřízené kartu komponenty nejsou explicitně předány jako parametry na kartě nastavení.</span><span class="sxs-lookup"><span data-stu-id="ecbde-355">The child Tab components aren't explicitly passed as parameters to the Tab Set.</span></span> <span data-ttu-id="ecbde-356">Místo toho podřízené kartu komponenty jsou součástí podřízenému obsahu ovládacího prvku na kartě nastavení.</span><span class="sxs-lookup"><span data-stu-id="ecbde-356">Instead, the child Tab components are part of the child content of the Tab Set.</span></span> <span data-ttu-id="ecbde-357">Však nastavení kartu stále potřebuje vědět o jednotlivých součástech kartu tak, aby ho může mít za následek záhlaví a na aktivní kartě. Povolit koordinace bez potřeby dalšího kódu, nastavte kartu komponenty *samotný můžete zadat jako hodnotu kaskádové* , který se potom vybere potomka kartu komponenty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-357">However, the Tab Set still needs to know about each Tab component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the Tab Set component *can provide itself as a cascading value* that is then picked up by the descendent Tab components.</span></span>

<span data-ttu-id="ecbde-358">*Components/TabSet.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-358">*Components/TabSet.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

<span data-ttu-id="ecbde-359">Podřízené součásti zachycení kartu obsahující kartu nastavit jako parametr šablony, karta součásti přidaly na kartu Nastavení a souřadnice na které kartě je aktivní.</span><span class="sxs-lookup"><span data-stu-id="ecbde-359">The descendent Tab components capture the containing Tab Set as a cascading parameter, so the Tab components add themselves to the Tab Set and coordinate on which tab is active.</span></span>

<span data-ttu-id="ecbde-360">*Components/Tab.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-360">*Components/Tab.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a><span data-ttu-id="ecbde-361">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="ecbde-361">Razor templates</span></span>

<span data-ttu-id="ecbde-362">Vykreslení fragmenty lze definovat pomocí syntaxe šablon Razor.</span><span class="sxs-lookup"><span data-stu-id="ecbde-362">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="ecbde-363">Šablony Razor představují způsob, jak definovat fragment kódu uživatelského rozhraní a předpokládají následující formát:</span><span class="sxs-lookup"><span data-stu-id="ecbde-363">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="ecbde-364">Následující příklad ukazuje, jak určit `RenderFragment` a `RenderFragment<T>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ecbde-364">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="ecbde-365">*RazorTemplates.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="ecbde-365">*RazorTemplates.cshtml*:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="ecbde-366">Vykreslení fragmenty definované pomocí syntaxe Razor šablony mohou být předány jako argumenty bez vizuálního vzhledu součásti nebo vykresluje přímo.</span><span class="sxs-lookup"><span data-stu-id="ecbde-366">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="ecbde-367">Předchozí šablony jsou přímo vykreslí následujícím kódem Razor:</span><span class="sxs-lookup"><span data-stu-id="ecbde-367">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="ecbde-368">Vykresleného výstupu:</span><span class="sxs-lookup"><span data-stu-id="ecbde-368">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="ecbde-369">Ruční RenderTreeBuilder logiky</span><span class="sxs-lookup"><span data-stu-id="ecbde-369">Manual RenderTreeBuilder logic</span></span>

`Microsoft.AspNetCore.Components.RenderTree` <span data-ttu-id="ecbde-370">poskytuje metody pro manipulaci s komponentami a prvky, včetně sestavení součástí ručně v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-370">provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="ecbde-371">Použití `RenderTreeBuilder` vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="ecbde-371">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="ecbde-372">Poškozená součásti (například značku neuzavřený značek) může způsobit nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="ecbde-372">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="ecbde-373">Vezměte v úvahu následující komponenty domácí mazlíček podrobnosti (*PetDetails.razor* součástí Razor; *PetDetails.cshtml* v Blazor), které ručně se dají do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="ecbde-373">Consider the following Pet Details component (*PetDetails.razor* in Razor Components; *PetDetails.cshtml* in Blazor), which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="ecbde-374">V následujícím příkladu, smyčky v `CreateComponent` metoda generuje tři komponenty domácí mazlíček podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="ecbde-374">In the following example, the loop in the `CreateComponent` method generates three Pet Details components.</span></span> <span data-ttu-id="ecbde-375">Při volání metody `RenderTreeBuilder` metody vytvoření součásti (`OpenComponent` a `AddAttribute`), pořadová čísla jsou čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-375">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="ecbde-376">Razor komponenty, které algoritmus rozdíl spoléhá na pořadová čísla odpovídající na různé řádky kódu, nikoli odlišné volání volání.</span><span class="sxs-lookup"><span data-stu-id="ecbde-376">The Razor Components difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="ecbde-377">Při vytváření komponent pomocí `RenderTreeBuilder` metody, pevně argumenty pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="ecbde-377">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> **<span data-ttu-id="ecbde-378">Použití výpočtu nebo čítače k vygenerování pořadové číslo může vést ke špatnému výkonu.</span><span class="sxs-lookup"><span data-stu-id="ecbde-378">Using a calculation or counter to generate the sequence number can lead to poor performance.</span></span>**

<span data-ttu-id="ecbde-379">Integrované komponenty (*BuiltContent.razor* součástí Razor; *BuiltContent.cshtml* v Blazor):</span><span class="sxs-lookup"><span data-stu-id="ecbde-379">Built component (*BuiltContent.razor* in Razor Components; *BuiltContent.cshtml* in Blazor):</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" onclick="@RenderComponent">
    Create three Pet Details components
</button>

@functions {
    RenderFragment CustomRender { get; set; }
    
    RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```
