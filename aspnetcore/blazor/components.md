---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/02/2019
uid: blazor/components
ms.openlocfilehash: c5525542516d7b1318c26d12a5f59b0ded8dc659
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819775"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="c2213-103">Vytváření a používání ASP.NET Corech komponent Razor</span><span class="sxs-lookup"><span data-stu-id="c2213-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="c2213-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c2213-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c2213-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c2213-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c2213-106">Aplikace Blazor jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="c2213-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="c2213-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="c2213-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="c2213-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c2213-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="c2213-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="c2213-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="c2213-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="c2213-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="c2213-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="c2213-111">Component classes</span></span>

<span data-ttu-id="c2213-112">Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="c2213-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="c2213-113">Komponenta v Blazor je formálně označována jako *Komponenta Razor*.</span><span class="sxs-lookup"><span data-stu-id="c2213-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="c2213-114">Komponenty mohou být vytvořeny pomocí přípony souboru *. cshtml* , pokud jsou soubory označeny jako soubory komponenty Razor pomocí `_RazorComponentInclude` vlastnosti MSBuild.</span><span class="sxs-lookup"><span data-stu-id="c2213-114">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="c2213-115">Například aplikace, která určuje, že všechny soubory *. cshtml* ve složce *Pages* by měly být považovány za soubory součástí Razor:</span><span class="sxs-lookup"><span data-stu-id="c2213-115">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="c2213-116">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="c2213-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="c2213-117">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="c2213-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="c2213-118">Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="c2213-119">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="c2213-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="c2213-120">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="c2213-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="c2213-121">`@code` V bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="c2213-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="c2213-122">Je přípustný více `@code` než jeden blok.</span><span class="sxs-lookup"><span data-stu-id="c2213-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="c2213-123">V předchozích verzích Preview ASP.NET Core 3,0 `@functions` byly bloky použity pro stejný účel jako `@code` bloky v součástech Razor.</span><span class="sxs-lookup"><span data-stu-id="c2213-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="c2213-124">`@functions`bloky nadále fungují v součástech Razor, ale doporučujeme použít `@code` blok v ASP.NET Core 3,0 Preview 6 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="c2213-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="c2213-125">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají `@`na.</span><span class="sxs-lookup"><span data-stu-id="c2213-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="c2213-126">Například C# pole se vykreslí pomocí předpony `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="c2213-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="c2213-127">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="c2213-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="c2213-128">`_headingFontStyle`do hodnoty vlastnosti CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="c2213-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="c2213-129">`_headingText`k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="c2213-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="c2213-130">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="c2213-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="c2213-131">Blazor pak porovná nový strom vykreslování s předchozí a použije všechny změny v model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="c2213-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="c2213-132">Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="c2213-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="c2213-133">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="c2213-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="c2213-134">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="c2213-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="c2213-135">Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky do nadřazené komponenty nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2213-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="c2213-136">Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je `WebApplication`kořenový obor názvů aplikace:</span><span class="sxs-lookup"><span data-stu-id="c2213-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="c2213-137">Integrace součástí do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="c2213-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="c2213-138">Použijte komponenty se stávajícími Razor Pages a MVC aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="c2213-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="c2213-139">Aby bylo možné použít součásti Razor, není nutné přepsat existující stránky ani zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c2213-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="c2213-140">Po vykreslení stránky nebo zobrazení jsou komponenty předem vygenerovány ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="c2213-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="c2213-141">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte `RenderComponentAsync<TComponent>` pomocnou metodu HTML:</span><span class="sxs-lookup"><span data-stu-id="c2213-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="c2213-142">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="c2213-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="c2213-143">Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="c2213-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="c2213-144">Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="c2213-145">Další informace o tom, jak se komponenty vykreslují a stav komponenty se spravuje v Blazorch aplikacích na straně serveru, <xref:blazor/hosting-models> najdete v článku.</span><span class="sxs-lookup"><span data-stu-id="c2213-145">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="using-components"></a><span data-ttu-id="c2213-146">Používání komponent</span><span class="sxs-lookup"><span data-stu-id="c2213-146">Using components</span></span>

<span data-ttu-id="c2213-147">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="c2213-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="c2213-148">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="c2213-149">Následující kód v *indexu. Razor* vykreslí `HeadingComponent` instanci:</span><span class="sxs-lookup"><span data-stu-id="c2213-149">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="c2213-150">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2213-150">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

## <a name="component-parameters"></a><span data-ttu-id="c2213-151">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="c2213-151">Component parameters</span></span>

<span data-ttu-id="c2213-152">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí vlastností (obvykle *neveřejné*) `[Parameter]` třídy komponenty s atributem.</span><span class="sxs-lookup"><span data-stu-id="c2213-152">Components can have *component parameters*, which are defined using properties (usually *non-public*) on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="c2213-153">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="c2213-153">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="c2213-154">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2213-154">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="c2213-155">V následujícím příkladu `ParentComponent` nastaví hodnotu `Title` vlastnosti `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="c2213-155">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="c2213-156">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2213-156">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="c2213-157">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="c2213-157">Child content</span></span>

<span data-ttu-id="c2213-158">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-158">Components can set the content of another component.</span></span> <span data-ttu-id="c2213-159">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="c2213-159">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="c2213-160">V následujícím příkladu `ChildComponent` `ChildContent` má vlastnost, která představuje `RenderFragment`, který představuje segment uživatelského rozhraní pro vykreslení.</span><span class="sxs-lookup"><span data-stu-id="c2213-160">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="c2213-161">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="c2213-161">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="c2213-162">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v `panel-body`panelu Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="c2213-162">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="c2213-163">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2213-163">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="c2213-164">Vlastnost, která přijímá `RenderFragment` obsah, musí být `ChildContent` pojmenována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="c2213-164">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="c2213-165">Následující `ParentComponent` obsah vám může poskytnout obsah pro `ChildComponent` vykreslování `<ChildComponent>` umístěním obsahu uvnitř značek.</span><span class="sxs-lookup"><span data-stu-id="c2213-165">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="c2213-166">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2213-166">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="c2213-167">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="c2213-167">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="c2213-168">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="c2213-168">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="c2213-169">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí [@attributes](xref:mvc/views/razor#attributes) direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="c2213-169">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="c2213-170">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="c2213-170">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="c2213-171">Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="c2213-171">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="c2213-172">V `<input>` následujícím příkladu první prvek (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="c2213-172">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    private string Maxlength { get; set; } = "10";

    [Parameter]
    private string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    private string Required { get; set; } = "required";

    [Parameter]
    private string Size { get; set; } = "50";

    [Parameter]
    private Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "true" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="c2213-173">Typ parametru musí být implementován `IEnumerable<KeyValuePair<string, object>>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="c2213-173">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="c2213-174">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="c2213-174">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="c2213-175">Vykreslené `<input>` elementy pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="c2213-175">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="true"
       size="50">
```

<span data-ttu-id="c2213-176">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí `[Parameter]` atributu `CaptureUnmatchedValues` s vlastností nastavenou na `true`:</span><span class="sxs-lookup"><span data-stu-id="c2213-176">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    private Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="c2213-177">`CaptureUnmatchedValues` Vlastnost on`[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="c2213-177">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="c2213-178">Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="c2213-178">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="c2213-179">Typ vlastnosti používaný pomocí `CaptureUnmatchedValues` musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="c2213-179">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="c2213-180">`IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="c2213-180">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="c2213-181">Datová vazba</span><span class="sxs-lookup"><span data-stu-id="c2213-181">Data binding</span></span>

<span data-ttu-id="c2213-182">Datové vazby na součásti a elementy modelu DOM jsou provedeny [@bind](xref:mvc/views/razor#bind) atributem.</span><span class="sxs-lookup"><span data-stu-id="c2213-182">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="c2213-183">V následujícím příkladu je svázáno `_italicsCheck` pole se zaškrtnutým stavem zaškrtávací políčko:</span><span class="sxs-lookup"><span data-stu-id="c2213-183">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="c2213-184">Když je políčko zaškrtnuté a políčko je zaškrtnuté, hodnota vlastnosti se aktualizuje na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="c2213-184">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="c2213-185">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c2213-185">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="c2213-186">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se v uživatelském rozhraní obvykle projeví aktualizace vlastností.</span><span class="sxs-lookup"><span data-stu-id="c2213-186">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="c2213-187">Použití `@bind` s`CurrentValue` vlastností(`<input @bind="CurrentValue" />`) je v podstatě ekvivalentem následujícího:</span><span class="sxs-lookup"><span data-stu-id="c2213-187">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="c2213-188">Při vykreslení `value` komponenty `CurrentValue` z vlastnosti input element přichází.</span><span class="sxs-lookup"><span data-stu-id="c2213-188">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="c2213-189">Když uživatel zadá do textového pole, `onchange` událost se aktivuje `CurrentValue` a vlastnost je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="c2213-189">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="c2213-190">Ve skutečnosti je generování kódu trochu složitější, protože `@bind` zpracovává několik případů, kde jsou prováděny převody typu.</span><span class="sxs-lookup"><span data-stu-id="c2213-190">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="c2213-191">V zásadě `@bind` přidruží aktuální hodnotu výrazu `value` k atributu a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="c2213-191">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="c2213-192">`onchange` Kromě zpracování[@bind-value:event](xref:mvc/views/razor#bind)událostí pomocí `@bind` syntaxe lze vlastnost nebo pole svázat pomocí [@bind-value](xref:mvc/views/razor#bind) jiných událostí `event` zadáním atributu s parametrem ().</span><span class="sxs-lookup"><span data-stu-id="c2213-192">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="c2213-193">Následující příklad váže `CurrentValue` vlastnost `oninput` pro událost:</span><span class="sxs-lookup"><span data-stu-id="c2213-193">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="c2213-194">Na rozdíl `onchange`od, která je aktivována, když prvek ztratí `oninput` fokus, je aktivována při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="c2213-194">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="c2213-195">**Řetězce formátu**</span><span class="sxs-lookup"><span data-stu-id="c2213-195">**Format strings**</span></span>

<span data-ttu-id="c2213-196">Datové vazby fungují s <xref:System.DateTime> řetězci formátu pomocí [@bind:format](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="c2213-196">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="c2213-197">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c2213-197">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c2213-198">Atribut určuje formát data, který se má použít `value` pro `<input>` element. `@bind:format`</span><span class="sxs-lookup"><span data-stu-id="c2213-198">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c2213-199">Formát je také použit k analýze hodnoty při `onchange` výskytu události.</span><span class="sxs-lookup"><span data-stu-id="c2213-199">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c2213-200">**Parametry součásti**</span><span class="sxs-lookup"><span data-stu-id="c2213-200">**Component parameters**</span></span>

<span data-ttu-id="c2213-201">Vazba rozpoznává parametry komponenty, `@bind-{property}` kde může svázat hodnotu vlastnosti napříč komponentami.</span><span class="sxs-lookup"><span data-stu-id="c2213-201">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="c2213-202">Následující podřízená komponenta (`ChildComponent`) `Year` má parametr komponenty a `YearChanged` zpětné volání:</span><span class="sxs-lookup"><span data-stu-id="c2213-202">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="c2213-203">`EventCallback<T>`je vysvětleno v části [vnořenou eventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="c2213-203">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="c2213-204">Následující nadřazená komponenta používá `ChildComponent` a váže `ParentYear` parametr `Year` z nadřazené položky k parametru v podřízené komponentě:</span><span class="sxs-lookup"><span data-stu-id="c2213-204">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
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

<span data-ttu-id="c2213-205">Načítání kódu `ParentComponent` generuje následující značky:</span><span class="sxs-lookup"><span data-stu-id="c2213-205">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="c2213-206">Pokud je hodnota `ParentYear` vlastnosti změněna výběrem tlačítka `ParentComponent`v `ChildComponent` , `Year` vlastnost je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="c2213-206">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="c2213-207">Nová hodnota `Year` se vykreslí v uživatelském rozhraní, `ParentComponent` když se znovu vykreslí:</span><span class="sxs-lookup"><span data-stu-id="c2213-207">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="c2213-208">Parametr je svázán, protože má doprovodnou `YearChanged` událost, která `Year` odpovídá typu parametru. `Year`</span><span class="sxs-lookup"><span data-stu-id="c2213-208">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="c2213-209">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` má v podstatě ekvivalent zápisu:</span><span class="sxs-lookup"><span data-stu-id="c2213-209">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="c2213-210">Obecně platí, že vlastnost může být svázána s odpovídající obslužnou rutinou `@bind-property:event` události pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="c2213-210">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="c2213-211">Vlastnost `MyProp` může být například svázána s `MyEventHandler` použitím následujících dvou atributů:</span><span class="sxs-lookup"><span data-stu-id="c2213-211">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="c2213-212">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="c2213-212">Event handling</span></span>

<span data-ttu-id="c2213-213">Komponenty Razor poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="c2213-213">Razor components provide event handling features.</span></span> <span data-ttu-id="c2213-214">Pro atribut elementu HTML s názvem `on{event}` ( `onclick` například a `onsubmit`) s hodnotou typu delegáta, komponenty Razor považují hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="c2213-214">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="c2213-215">Název atributu má vždycky formát [ @on{Event}](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="c2213-215">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="c2213-216">Následující kód volá `UpdateHeading` metodu, pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c2213-216">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="c2213-217">Následující kód volá `CheckChanged` metodu, když je zaškrtávací políčko změněno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c2213-217">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="c2213-218">Obslužné rutiny událostí mohou být také asynchronní a <xref:System.Threading.Tasks.Task>vracet.</span><span class="sxs-lookup"><span data-stu-id="c2213-218">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="c2213-219">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="c2213-219">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="c2213-220">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="c2213-220">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="c2213-221">V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="c2213-221">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="c2213-222">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="c2213-222">Event argument types</span></span>

<span data-ttu-id="c2213-223">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="c2213-223">For some events, event argument types are permitted.</span></span> <span data-ttu-id="c2213-224">Pokud přístup k některému z těchto typů událostí není nezbytný, není nutné ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="c2213-224">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="c2213-225">Podporované [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="c2213-225">Supported [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs) are shown in the following table.</span></span>

| <span data-ttu-id="c2213-226">Událost</span><span class="sxs-lookup"><span data-stu-id="c2213-226">Event</span></span> | <span data-ttu-id="c2213-227">Třída</span><span class="sxs-lookup"><span data-stu-id="c2213-227">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="c2213-228">Schránka</span><span class="sxs-lookup"><span data-stu-id="c2213-228">Clipboard</span></span> | `UIClipboardEventArgs` |
| <span data-ttu-id="c2213-229">Přetažení</span><span class="sxs-lookup"><span data-stu-id="c2213-229">Drag</span></span>  | <span data-ttu-id="c2213-230">`UIDragEventArgs`slouží k uchovávání přetažených dat během operace přetažení a může obsahovat jeden nebo více `UIDataTransferItem`. &ndash; `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="c2213-230">`UIDragEventArgs` &ndash; `DataTransfer` is used to hold the dragged data during a drag and drop operation and may hold one or more `UIDataTransferItem`.</span></span> <span data-ttu-id="c2213-231">`UIDataTransferItem`reprezentuje jednu položku dat přetažením.</span><span class="sxs-lookup"><span data-stu-id="c2213-231">`UIDataTransferItem` represents one drag data item.</span></span> |
| <span data-ttu-id="c2213-232">Chyba</span><span class="sxs-lookup"><span data-stu-id="c2213-232">Error</span></span> | `UIErrorEventArgs` |
| <span data-ttu-id="c2213-233">Vybrána</span><span class="sxs-lookup"><span data-stu-id="c2213-233">Focus</span></span> | <span data-ttu-id="c2213-234">`UIFocusEventArgs`Nezahrnuje podporu pro `relatedTarget`. &ndash;</span><span class="sxs-lookup"><span data-stu-id="c2213-234">`UIFocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="c2213-235">`<input>`mění</span><span class="sxs-lookup"><span data-stu-id="c2213-235">`<input>` change</span></span> | `UIChangeEventArgs` |
| <span data-ttu-id="c2213-236">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="c2213-236">Keyboard</span></span> | `UIKeyboardEventArgs` |
| <span data-ttu-id="c2213-237">Stisknut</span><span class="sxs-lookup"><span data-stu-id="c2213-237">Mouse</span></span> | `UIMouseEventArgs` |
| <span data-ttu-id="c2213-238">Ukazatele myši</span><span class="sxs-lookup"><span data-stu-id="c2213-238">Mouse pointer</span></span> | `UIPointerEventArgs` |
| <span data-ttu-id="c2213-239">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="c2213-239">Mouse wheel</span></span> | `UIWheelEventArgs` |
| <span data-ttu-id="c2213-240">Průběh</span><span class="sxs-lookup"><span data-stu-id="c2213-240">Progress</span></span> | `UIProgressEventArgs` |
| <span data-ttu-id="c2213-241">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="c2213-241">Touch</span></span> | <span data-ttu-id="c2213-242">`UITouchEventArgs`&ndash; představujejedenkontaktníbodnazařízenícitlivém`UITouchPoint` na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="c2213-242">`UITouchEventArgs` &ndash; `UITouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="c2213-243">Informace o vlastnostech a chování zpracování událostí událostí v předchozí tabulce naleznete v tématu [třídy EventArgs ve zdroji odkazu](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src).</span><span class="sxs-lookup"><span data-stu-id="c2213-243">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="c2213-244">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="c2213-244">Lambda expressions</span></span>

<span data-ttu-id="c2213-245">Lambda výrazy lze také použít:</span><span class="sxs-lookup"><span data-stu-id="c2213-245">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="c2213-246">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="c2213-246">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="c2213-247">Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a jeho čísla tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c2213-247">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="c2213-248">Nepoužívejte proměnnou smyčky ( `i`) ve `for` smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="c2213-248">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="c2213-249">V opačném případě se stejná proměnná používá ve všech výrazech lambda, což způsobuje `i`, že hodnota je stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="c2213-249">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="c2213-250">Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="c2213-250">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="c2213-251">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="c2213-251">EventCallback</span></span>

<span data-ttu-id="c2213-252">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde&mdash;k události podřízené komponenty, například když dojde k události v podřízeném objektu. `onclick`</span><span class="sxs-lookup"><span data-stu-id="c2213-252">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="c2213-253">Chcete-li zobrazit události napříč komponentami `EventCallback`, použijte.</span><span class="sxs-lookup"><span data-stu-id="c2213-253">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="c2213-254">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="c2213-254">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="c2213-255">V ukázkové aplikaci ukazuje, jak `EventCallback` je nastavena `onclick` obslužná rutina tlačítka pro příjem delegáta z ukázky `ParentComponent`. `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="c2213-255">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="c2213-256">Je zadaný s `UIMouseEventArgs` ,`onclick` který je vhodný pro událost z periferního zařízení: `EventCallback`</span><span class="sxs-lookup"><span data-stu-id="c2213-256">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="c2213-257">Nastaví podřízený objekt `ShowMessage`najehometodu: `EventCallback<T>` `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="c2213-257">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="c2213-258">Když je vybráno tlačítko v `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="c2213-258">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="c2213-259">`ParentComponent`Je volána metoda.`ShowMessage`</span><span class="sxs-lookup"><span data-stu-id="c2213-259">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="c2213-260">`messageText`se aktualizuje a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="c2213-260">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="c2213-261">Volání `StateHasChanged` není vyžadováno v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="c2213-261">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="c2213-262">`StateHasChanged`je volána automaticky pro `ParentComponent`revykreslování, stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="c2213-262">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="c2213-263">`EventCallback`a `EventCallback<T>` povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="c2213-263">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="c2213-264">`EventCallback<T>`je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="c2213-264">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="c2213-265">`EventCallback`je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="c2213-265">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="c2213-266">`EventCallback` Vyvolat nebo `EventCallback<T>` s a`InvokeAsync` očekávat :<xref:System.Threading.Tasks.Task></span><span class="sxs-lookup"><span data-stu-id="c2213-266">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="c2213-267">Použití `EventCallback` a`EventCallback<T>` pro zpracování událostí a parametry komponenty vazby.</span><span class="sxs-lookup"><span data-stu-id="c2213-267">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="c2213-268">Preferovat silného typu `EventCallback<T>` přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="c2213-268">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="c2213-269">`EventCallback<T>`poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-269">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="c2213-270">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="c2213-270">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="c2213-271">Použijte `EventCallback` v případě, že zpětnému volání není předáno žádné číslo.</span><span class="sxs-lookup"><span data-stu-id="c2213-271">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="c2213-272">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="c2213-272">Capture references to components</span></span>

<span data-ttu-id="c2213-273">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="c2213-273">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="c2213-274">Chcete-li zachytit odkaz na komponentu, [@ref](xref:mvc/views/razor#ref) přidejte atribut do podřízené komponenty a pak definujte pole se stejným názvem a stejným typem jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="c2213-274">To capture a component reference, add a [@ref](xref:mvc/views/razor#ref) attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

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

<span data-ttu-id="c2213-275">Při vykreslení `loginDialog` komponenty je pole vyplněno `MyLoginDialog` instancí podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-275">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="c2213-276">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-276">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c2213-277">Proměnná je naplněna pouze po vykreslení komponenty a její výstup `MyLoginDialog` obsahuje element. `loginDialog`</span><span class="sxs-lookup"><span data-stu-id="c2213-277">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="c2213-278">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="c2213-278">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="c2213-279">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte `OnAfterRenderAsync` metody `OnAfterRender` nebo.</span><span class="sxs-lookup"><span data-stu-id="c2213-279">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="c2213-280">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není to funkce [interoperability JavaScriptu](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="c2213-280">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="c2213-281">Odkazy na součásti nejsou předány kódu&mdash;jazyka JavaScript, které jsou používány pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="c2213-281">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="c2213-282">Nepoužívejte odkazy na součásti pro použití stavu podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="c2213-282">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="c2213-283">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="c2213-283">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="c2213-284">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="c2213-284">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="c2213-285">Použití \@klíče k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="c2213-285">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="c2213-286">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazor rozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="c2213-286">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="c2213-287">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="c2213-287">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="c2213-288">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="c2213-288">Consider the following example:</span></span>

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

<span data-ttu-id="c2213-289">Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="c2213-289">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="c2213-290">Když se komponenta znovu vykreslí, může `<DetailsEditor>` se tato součást změnit, aby `Details` přijímala jiné hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="c2213-290">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="c2213-291">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="c2213-291">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="c2213-292">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="c2213-292">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="c2213-293">Proces mapování lze řídit pomocí `@key` atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="c2213-293">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="c2213-294">`@key`způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="c2213-294">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="c2213-295">Když se `People` kolekce změní, rozdílový algoritmus zachovává přidružení mezi `<DetailsEditor>` instancemi a `person` instancemi:</span><span class="sxs-lookup"><span data-stu-id="c2213-295">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="c2213-296">Pokud se ze seznamu odstraní, z uživatelského rozhraní se odebere `<DetailsEditor>` jenom odpovídající instance. `People` `Person`</span><span class="sxs-lookup"><span data-stu-id="c2213-296">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="c2213-297">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="c2213-297">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c2213-298">Pokud je vložena na nějaké místo v seznamu, je do příslušné `<DetailsEditor>` pozice vložena jedna nová instance. `Person`</span><span class="sxs-lookup"><span data-stu-id="c2213-298">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="c2213-299">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="c2213-299">Other instances are left unchanged.</span></span>
* <span data-ttu-id="c2213-300">Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c2213-300">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="c2213-301">V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="c2213-301">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c2213-302">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="c2213-302">Keys are local to each container element or component.</span></span> <span data-ttu-id="c2213-303">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="c2213-303">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="c2213-304">Kdy použít \@klíč</span><span class="sxs-lookup"><span data-stu-id="c2213-304">When to use \@key</span></span>

<span data-ttu-id="c2213-305">Obvykle má smysl použít `@key` při každém vykreslení seznamu (například `@foreach` v bloku) a existuje vhodná `@key`hodnota pro definování.</span><span class="sxs-lookup"><span data-stu-id="c2213-305">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="c2213-306">Můžete také použít `@key` , chcete-li zabránit Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="c2213-306">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="c2213-307">Pokud `@currentPerson` se změní `@key` , direktiva Attribute vynutí, aby Blazor `<div>` zahození celého a jeho následníků a znovu sestavil podstrom v uživatelském rozhraní s novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="c2213-307">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="c2213-308">To může být užitečné, pokud potřebujete zaručit, že se při `@currentPerson` změnách nezachovají stav uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c2213-308">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="c2213-309">Kdy nepoužívat \@klíč</span><span class="sxs-lookup"><span data-stu-id="c2213-309">When not to use \@key</span></span>

<span data-ttu-id="c2213-310">Při rozdílech s se `@key`účtují náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="c2213-310">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="c2213-311">Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="c2213-311">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="c2213-312">`@key` I když se nepoužívá, Blazor zachová podřízený element a instance komponenty co nejvíc.</span><span class="sxs-lookup"><span data-stu-id="c2213-312">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="c2213-313">Jedinou výhodou použití `@key` je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="c2213-313">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="c2213-314">Jaké hodnoty se mají použít \@pro klíč</span><span class="sxs-lookup"><span data-stu-id="c2213-314">What values to use for \@key</span></span>

<span data-ttu-id="c2213-315">Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro `@key`:</span><span class="sxs-lookup"><span data-stu-id="c2213-315">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="c2213-316">Instance objektů modelu (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="c2213-316">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="c2213-317">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="c2213-317">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="c2213-318">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="c2213-318">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="c2213-319">Vyhněte se zadávání hodnoty, která může kolidovat neočekávaně.</span><span class="sxs-lookup"><span data-stu-id="c2213-319">Avoid supplying a value that can clash unexpectedly.</span></span> <span data-ttu-id="c2213-320">Pokud `@key="@someObject.GetHashCode()"` je zadaný, může dojít k neočekávaným konfliktům, protože kódy hash nesouvisejících objektů můžou být stejné.</span><span class="sxs-lookup"><span data-stu-id="c2213-320">If `@key="@someObject.GetHashCode()"` is supplied, unexpected clashes may occur because the hash codes of unrelated objects can be the same.</span></span> <span data-ttu-id="c2213-321">Pokud jsou `@key` hodnoty v konfliktu požadovány v rámci stejné nadřazené položky `@key` , hodnoty nebudou přijaty.</span><span class="sxs-lookup"><span data-stu-id="c2213-321">If clashing `@key` values are requested within the same parent, the `@key` values won't be honored.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="c2213-322">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="c2213-322">Lifecycle methods</span></span>

<span data-ttu-id="c2213-323">`OnInitAsync`a `OnInit` spusťte kód pro inicializaci komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-323">`OnInitAsync` and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="c2213-324">Chcete-li provést asynchronní operaci, `OnInitAsync` použijte `await` a klíčové slovo pro operaci:</span><span class="sxs-lookup"><span data-stu-id="c2213-324">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="c2213-325">Pro synchronní operaci použijte `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="c2213-325">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="c2213-326">`OnParametersSetAsync`a `OnParametersSet` jsou volány, když komponenta přijímá parametry z nadřazené položky a hodnoty jsou přiřazeny vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="c2213-326">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="c2213-327">Tyto metody jsou spouštěny po inicializaci komponenty a pokaždé, když je vykreslena komponenta:</span><span class="sxs-lookup"><span data-stu-id="c2213-327">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="c2213-328">`OnAfterRenderAsync`a `OnAfterRender` jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-328">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="c2213-329">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="c2213-329">Element and component references are populated at this point.</span></span> <span data-ttu-id="c2213-330">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="c2213-330">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

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

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="c2213-331">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="c2213-331">Handle incomplete async actions at render</span></span>

<span data-ttu-id="c2213-332">Asynchronní akce provedené v událostech životního cyklu se možná nedokončily, než se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="c2213-332">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="c2213-333">Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty.</span><span class="sxs-lookup"><span data-stu-id="c2213-333">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="c2213-334">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="c2213-334">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="c2213-335">Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), `null`zatímco objekty jsou.</span><span class="sxs-lookup"><span data-stu-id="c2213-335">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="c2213-336">V komponentě `OnInitAsync` šablon Blazor je přepsáno na asynchronně příjem dat předpovědi (`forecasts`). `FetchData`</span><span class="sxs-lookup"><span data-stu-id="c2213-336">In the `FetchData` component of the Blazor templates, `OnInitAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="c2213-337">V takovém případě `forecasts` se uživateli zobrazí zpráva o načítání. `null`</span><span class="sxs-lookup"><span data-stu-id="c2213-337">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="c2213-338">`Task` Po úspěšnémdokončenísekomponentaznovuvykreslís`OnInitAsync` aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="c2213-338">After the `Task` returned by `OnInitAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="c2213-339">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2213-339">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="c2213-340">Spustit kód před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="c2213-340">Execute code before parameters are set</span></span>

<span data-ttu-id="c2213-341">`SetParameters`lze přepsat pro spuštění kódu před nastavením parametrů:</span><span class="sxs-lookup"><span data-stu-id="c2213-341">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="c2213-342">Pokud `base.SetParameters` není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="c2213-342">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="c2213-343">Například příchozí parametry nemusejí být přiřazeny vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="c2213-343">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="c2213-344">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="c2213-344">Suppress refreshing of the UI</span></span>

<span data-ttu-id="c2213-345">`ShouldRender`lze přepsat pro potlačení aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c2213-345">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="c2213-346">Pokud se implementace vrátí `true`, uživatelské rozhraní se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="c2213-346">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="c2213-347">I když `ShouldRender` je přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="c2213-347">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="c2213-348">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="c2213-348">Component disposal with IDisposable</span></span>

<span data-ttu-id="c2213-349">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c2213-349">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="c2213-350">Následující komponenta používá `@implements IDisposable` `Dispose` a metodu:</span><span class="sxs-lookup"><span data-stu-id="c2213-350">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="c2213-351">Směrování</span><span class="sxs-lookup"><span data-stu-id="c2213-351">Routing</span></span>

<span data-ttu-id="c2213-352">Směrování v Blazor se dosahuje tak, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="c2213-352">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="c2213-353">Když je zkompilován soubor Razor s `@page` direktivou, vygenerovaná třída je <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> dána zadáním šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="c2213-353">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="c2213-354">V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="c2213-354">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="c2213-355">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="c2213-355">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="c2213-356">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="c2213-356">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="c2213-357">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="c2213-357">Route parameters</span></span>

<span data-ttu-id="c2213-358">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v `@page` direktivě.</span><span class="sxs-lookup"><span data-stu-id="c2213-358">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="c2213-359">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="c2213-359">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="c2213-360">*Komponenta parametru směrování*:</span><span class="sxs-lookup"><span data-stu-id="c2213-360">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="c2213-361">Volitelné parametry nejsou podporované, takže se `@page` v předchozím příkladu použijí dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="c2213-361">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="c2213-362">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="c2213-362">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="c2213-363">Druhá `@page` direktiva `{text}` převezme parametr Route a `Text` přiřadí hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c2213-363">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="c2213-364">Dědičnost základní třídy pro prostředí s kódem na pozadí</span><span class="sxs-lookup"><span data-stu-id="c2213-364">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="c2213-365">Soubory součástí kombinují značky HTML C# a zpracovávají kód ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="c2213-365">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="c2213-366">`@inherits` Direktiva se dá použít k poskytování aplikací Blazor s prostředím "kódu na pozadí", které odděluje označení komponenty od zpracování kódu.</span><span class="sxs-lookup"><span data-stu-id="c2213-366">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="c2213-367">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak může komponenta zdědit základní třídu, `BlazorRocksBase`k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-367">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="c2213-368">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="c2213-368">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="c2213-369">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="c2213-369">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="c2213-370">Základní třída by měla být odvozena od `ComponentBase`třídy.</span><span class="sxs-lookup"><span data-stu-id="c2213-370">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="c2213-371">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="c2213-371">Import components</span></span>

<span data-ttu-id="c2213-372">Obor názvů komponenty vytvořené pomocí Razor je založen na:</span><span class="sxs-lookup"><span data-stu-id="c2213-372">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="c2213-373">Projekt `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="c2213-373">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="c2213-374">Cesta z kořenového adresáře projektu k součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-374">The path from the project root to the component.</span></span> <span data-ttu-id="c2213-375">Například `ComponentsSample/Pages/Index.razor` je v oboru názvů `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="c2213-375">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="c2213-376">Komponenty následují C# pravidla vazeb názvů.</span><span class="sxs-lookup"><span data-stu-id="c2213-376">Components follow C# name binding rules.</span></span> <span data-ttu-id="c2213-377">V případě *indexu. Razor*jsou v oboru *ComponentsSample*všechny komponenty ve stejné složce, *stránkách*a nadřazené složce.</span><span class="sxs-lookup"><span data-stu-id="c2213-377">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="c2213-378">Komponenty definované v jiném oboru názvů lze převést do rozsahu pomocí direktivy [ \@using](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="c2213-378">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="c2213-379">Pokud ve složce `ComponentsSample/Shared/`existuje `NavMenu.razor`jiná součást, lze komponentu použít v `Index.razor` příkazu s následujícím `@using` příkazem:</span><span class="sxs-lookup"><span data-stu-id="c2213-379">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="c2213-380">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které odstraňují nutnost [ \@](xref:mvc/views/razor#using) direktivy using:</span><span class="sxs-lookup"><span data-stu-id="c2213-380">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="c2213-381">`global::` Kvalifikace není podporovaná.</span><span class="sxs-lookup"><span data-stu-id="c2213-381">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="c2213-382">Import komponent s `using` příkazy s aliasem ( `@using Foo = Bar`například) není podporován.</span><span class="sxs-lookup"><span data-stu-id="c2213-382">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="c2213-383">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="c2213-383">Partially qualified names aren't supported.</span></span> <span data-ttu-id="c2213-384">Například přidání `@using ComponentsSample` a `NavMenu.razor` odkazování pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="c2213-384">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="c2213-385">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="c2213-385">Conditional HTML element attributes</span></span>

<span data-ttu-id="c2213-386">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="c2213-386">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="c2213-387">Pokud je `false` hodnota nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="c2213-387">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="c2213-388">Pokud je `true`hodnota, je vygenerována hodnota atributu minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="c2213-388">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="c2213-389">V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="c2213-389">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="c2213-390">Pokud `IsCompleted` je`true`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="c2213-390">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="c2213-391">Pokud `IsCompleted` je`false`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="c2213-391">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="c2213-392">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="c2213-392">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="c2213-393">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="c2213-393">Raw HTML</span></span>

<span data-ttu-id="c2213-394">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="c2213-394">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="c2213-395">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="c2213-395">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="c2213-396">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="c2213-396">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="c2213-397">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="c2213-397">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="c2213-398">Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="c2213-398">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="c2213-399">Komponenty se šablonami</span><span class="sxs-lookup"><span data-stu-id="c2213-399">Templated components</span></span>

<span data-ttu-id="c2213-400">Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-400">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="c2213-401">Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-401">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="c2213-402">Mezi několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="c2213-402">A couple of examples include:</span></span>

* <span data-ttu-id="c2213-403">Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="c2213-403">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="c2213-404">Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="c2213-404">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="c2213-405">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="c2213-405">Template parameters</span></span>

<span data-ttu-id="c2213-406">Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo. `RenderFragment<T>`</span><span class="sxs-lookup"><span data-stu-id="c2213-406">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="c2213-407">Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="c2213-407">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="c2213-408">`RenderFragment<T>`převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.</span><span class="sxs-lookup"><span data-stu-id="c2213-408">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="c2213-409">`TableTemplate`část</span><span class="sxs-lookup"><span data-stu-id="c2213-409">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="c2213-410">Při použití šablonované komponenty lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="c2213-410">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="c2213-411">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="c2213-411">Template context parameters</span></span>

<span data-ttu-id="c2213-412">Argumenty součásti typu `RenderFragment<T>` předané jako elementy mají implicitní parametr s názvem `context` (například z předchozího příkladu `@context.PetId`kódu), ale můžete změnit název parametru pomocí `Context` atributu u podřízeného objektu. objekt.</span><span class="sxs-lookup"><span data-stu-id="c2213-412">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="c2213-413">V následujícím příkladu `RowTemplate` `Context` atribut prvku Určuje `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="c2213-413">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="c2213-414">Alternativně lze zadat `Context` atribut prvku komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-414">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="c2213-415">Zadaný `Context` atribut se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="c2213-415">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="c2213-416">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu).</span><span class="sxs-lookup"><span data-stu-id="c2213-416">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="c2213-417">V následujícím příkladu `Context` se atribut zobrazí `TableTemplate` na elementu a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="c2213-417">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="c2213-418">Komponenty s obecným typem</span><span class="sxs-lookup"><span data-stu-id="c2213-418">Generic-typed components</span></span>

<span data-ttu-id="c2213-419">Komponenty se šablonami jsou často typu obecně typované.</span><span class="sxs-lookup"><span data-stu-id="c2213-419">Templated components are often generically typed.</span></span> <span data-ttu-id="c2213-420">Například obecná `ListViewTemplate` komponenta může být použita k vykreslování `IEnumerable<T>` hodnot.</span><span class="sxs-lookup"><span data-stu-id="c2213-420">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="c2213-421">Chcete-li definovat obecné komponenty, použijte `@typeparam` direktivu k určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="c2213-421">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="c2213-422">Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="c2213-422">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="c2213-423">V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="c2213-423">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="c2213-424">V následujícím příkladu `TItem="Pet"` určuje typ:</span><span class="sxs-lookup"><span data-stu-id="c2213-424">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="c2213-425">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="c2213-425">Cascading values and parameters</span></span>

<span data-ttu-id="c2213-426">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="c2213-426">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="c2213-427">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="c2213-427">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="c2213-428">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="c2213-428">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="c2213-429">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="c2213-429">Theme example</span></span>

<span data-ttu-id="c2213-430">V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="c2213-430">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="c2213-431">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="c2213-431">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="c2213-432">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="c2213-432">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="c2213-433">`CascadingValue` Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="c2213-433">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="c2213-434">Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="c2213-434">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="c2213-435">`ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="c2213-435">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="c2213-436">Každá odvozená komponenta může tuto vlastnost spotřebovat `ThemeInfo` prostřednictvím kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="c2213-436">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="c2213-437">`CascadingValuesParametersLayout`část</span><span class="sxs-lookup"><span data-stu-id="c2213-437">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="c2213-438">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí `[CascadingParameter]` atributu nebo na základě hodnoty řetězcového názvu:</span><span class="sxs-lookup"><span data-stu-id="c2213-438">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="c2213-439">Vazba s hodnotou názvu řetězce je relevantní, pokud máte více kaskádových hodnot stejného typu a potřebujete je odlišit v rámci stejného podstromu.</span><span class="sxs-lookup"><span data-stu-id="c2213-439">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="c2213-440">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="c2213-440">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="c2213-441">V ukázkové aplikaci `CascadingValuesParametersTheme` váže `ThemeInfo` komponenta kaskádovou hodnotu k kaskádovým parametrům.</span><span class="sxs-lookup"><span data-stu-id="c2213-441">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="c2213-442">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="c2213-442">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="c2213-443">`CascadingValuesParametersTheme`část</span><span class="sxs-lookup"><span data-stu-id="c2213-443">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
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

### <a name="tabset-example"></a><span data-ttu-id="c2213-444">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="c2213-444">TabSet example</span></span>

<span data-ttu-id="c2213-445">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="c2213-445">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="c2213-446">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c2213-446">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="c2213-447">Ukázková aplikace má `ITab` rozhraní, které implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="c2213-447">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="c2213-448">Komponenta používá komponentu, která obsahuje několik `Tab` komponent: `TabSet` `CascadingValuesParametersTabSet`</span><span class="sxs-lookup"><span data-stu-id="c2213-448">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="c2213-449">Podřízené `Tab` součásti nejsou explicitně předány jako parametry `TabSet`do.</span><span class="sxs-lookup"><span data-stu-id="c2213-449">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="c2213-450">Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu. `TabSet`</span><span class="sxs-lookup"><span data-stu-id="c2213-450">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="c2213-451">Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. `TabSet` Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu `TabSet` , komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta `Tab` podřízenými součástmi.</span><span class="sxs-lookup"><span data-stu-id="c2213-451">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="c2213-452">`TabSet`část</span><span class="sxs-lookup"><span data-stu-id="c2213-452">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="c2213-453">Podřízené komponenty `Tab` zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do `TabSet` souřadnice a, na které karty jsou aktivní.</span><span class="sxs-lookup"><span data-stu-id="c2213-453">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="c2213-454">`Tab`část</span><span class="sxs-lookup"><span data-stu-id="c2213-454">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="c2213-455">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="c2213-455">Razor templates</span></span>

<span data-ttu-id="c2213-456">Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor.</span><span class="sxs-lookup"><span data-stu-id="c2213-456">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="c2213-457">Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="c2213-457">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="c2213-458">Následující příklad ukazuje, jak zadat `RenderFragment` a `RenderFragment<T>` hodnoty a vykreslit šablony přímo v součásti.</span><span class="sxs-lookup"><span data-stu-id="c2213-458">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="c2213-459">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="c2213-459">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="c2213-460">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="c2213-460">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="c2213-461">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="c2213-461">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="c2213-462">`Microsoft.AspNetCore.Components.RenderTree`poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.</span><span class="sxs-lookup"><span data-stu-id="c2213-462">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="c2213-463">Použití aplikace `RenderTreeBuilder` k vytvoření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="c2213-463">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="c2213-464">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="c2213-464">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="c2213-465">Vezměte v úvahu `PetDetails` následující součást, kterou je možné ručně vytvořit do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="c2213-465">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    private string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="c2213-466">V následujícím příkladu smyčka v `CreateComponent` metodě generuje tři `PetDetails` komponenty.</span><span class="sxs-lookup"><span data-stu-id="c2213-466">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="c2213-467">Při volání `RenderTreeBuilder` metod pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="c2213-467">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="c2213-468">Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání.</span><span class="sxs-lookup"><span data-stu-id="c2213-468">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="c2213-469">Při vytváření komponenty s `RenderTreeBuilder` metodami nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="c2213-469">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="c2213-470">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="c2213-470">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="c2213-471">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="c2213-471">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="c2213-472">`BuiltContent`část</span><span class="sxs-lookup"><span data-stu-id="c2213-472">`BuiltContent` component:</span></span>

```cshtml
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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="c2213-473">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="c2213-473">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="c2213-474">Soubory `.razor` Blazor jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="c2213-474">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="c2213-475">Tato možnost je potenciálně skvělou výhodou `.razor` , protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="c2213-475">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="c2213-476">Hlavní příklad těchto vylepšení zahrnuje pořadová *čísla*.</span><span class="sxs-lookup"><span data-stu-id="c2213-476">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="c2213-477">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="c2213-477">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="c2213-478">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="c2213-478">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="c2213-479">Vezměte v úvahu následující `.razor` jednoduchý soubor:</span><span class="sxs-lookup"><span data-stu-id="c2213-479">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="c2213-480">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="c2213-480">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="c2213-481">V případě, že se kód spustí poprvé, v `someFlag` případě `true`, že je, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="c2213-481">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="c2213-482">Sequence</span><span class="sxs-lookup"><span data-stu-id="c2213-482">Sequence</span></span> | <span data-ttu-id="c2213-483">type</span><span class="sxs-lookup"><span data-stu-id="c2213-483">Type</span></span>      | <span data-ttu-id="c2213-484">Data</span><span class="sxs-lookup"><span data-stu-id="c2213-484">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c2213-485">0</span><span class="sxs-lookup"><span data-stu-id="c2213-485">0</span></span>        | <span data-ttu-id="c2213-486">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c2213-486">Text node</span></span> | <span data-ttu-id="c2213-487">První</span><span class="sxs-lookup"><span data-stu-id="c2213-487">First</span></span>  |
| <span data-ttu-id="c2213-488">1</span><span class="sxs-lookup"><span data-stu-id="c2213-488">1</span></span>        | <span data-ttu-id="c2213-489">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c2213-489">Text node</span></span> | <span data-ttu-id="c2213-490">Sekunda</span><span class="sxs-lookup"><span data-stu-id="c2213-490">Second</span></span> |

<span data-ttu-id="c2213-491">Představte `false`si, že `someFlag` se zobrazí a značka se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="c2213-491">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="c2213-492">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="c2213-492">This time, the builder receives:</span></span>

| <span data-ttu-id="c2213-493">Sequence</span><span class="sxs-lookup"><span data-stu-id="c2213-493">Sequence</span></span> | <span data-ttu-id="c2213-494">type</span><span class="sxs-lookup"><span data-stu-id="c2213-494">Type</span></span>       | <span data-ttu-id="c2213-495">Data</span><span class="sxs-lookup"><span data-stu-id="c2213-495">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="c2213-496">1</span><span class="sxs-lookup"><span data-stu-id="c2213-496">1</span></span>        | <span data-ttu-id="c2213-497">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c2213-497">Text node</span></span>  | <span data-ttu-id="c2213-498">Sekunda</span><span class="sxs-lookup"><span data-stu-id="c2213-498">Second</span></span> |

<span data-ttu-id="c2213-499">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:</span><span class="sxs-lookup"><span data-stu-id="c2213-499">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="c2213-500">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="c2213-500">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="c2213-501">Co je špatné, pokud generujete pořadová čísla programově</span><span class="sxs-lookup"><span data-stu-id="c2213-501">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="c2213-502">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="c2213-502">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="c2213-503">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="c2213-503">Now, the first output is:</span></span>

| <span data-ttu-id="c2213-504">Sequence</span><span class="sxs-lookup"><span data-stu-id="c2213-504">Sequence</span></span> | <span data-ttu-id="c2213-505">type</span><span class="sxs-lookup"><span data-stu-id="c2213-505">Type</span></span>      | <span data-ttu-id="c2213-506">Data</span><span class="sxs-lookup"><span data-stu-id="c2213-506">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="c2213-507">0</span><span class="sxs-lookup"><span data-stu-id="c2213-507">0</span></span>        | <span data-ttu-id="c2213-508">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c2213-508">Text node</span></span> | <span data-ttu-id="c2213-509">První</span><span class="sxs-lookup"><span data-stu-id="c2213-509">First</span></span>  |
| <span data-ttu-id="c2213-510">1</span><span class="sxs-lookup"><span data-stu-id="c2213-510">1</span></span>        | <span data-ttu-id="c2213-511">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c2213-511">Text node</span></span> | <span data-ttu-id="c2213-512">Sekunda</span><span class="sxs-lookup"><span data-stu-id="c2213-512">Second</span></span> |

<span data-ttu-id="c2213-513">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="c2213-513">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="c2213-514">`someFlag`je `false` ve druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="c2213-514">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="c2213-515">Sequence</span><span class="sxs-lookup"><span data-stu-id="c2213-515">Sequence</span></span> | <span data-ttu-id="c2213-516">type</span><span class="sxs-lookup"><span data-stu-id="c2213-516">Type</span></span>      | <span data-ttu-id="c2213-517">Data</span><span class="sxs-lookup"><span data-stu-id="c2213-517">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="c2213-518">0</span><span class="sxs-lookup"><span data-stu-id="c2213-518">0</span></span>        | <span data-ttu-id="c2213-519">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="c2213-519">Text node</span></span> | <span data-ttu-id="c2213-520">Sekunda</span><span class="sxs-lookup"><span data-stu-id="c2213-520">Second</span></span> |

<span data-ttu-id="c2213-521">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="c2213-521">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="c2213-522">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="c2213-522">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="c2213-523">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="c2213-523">Remove the second text node.</span></span>

<span data-ttu-id="c2213-524">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` byly větve a cykly přítomny v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="c2213-524">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="c2213-525">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="c2213-525">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="c2213-526">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="c2213-526">This is a trivial example.</span></span> <span data-ttu-id="c2213-527">Ve složitějších případech se složitými a hluboce vnořenými strukturami, a to hlavně pomocí smyček, náklady na výkon jsou závažnější.</span><span class="sxs-lookup"><span data-stu-id="c2213-527">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="c2213-528">Místo toho, aby se ihned identifikovaly, které bloky nebo větve smyček byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování a obvykle sestavovat mnohem delší úpravu skriptů, protože je nepřehledně neinformován o tom, jak staré a nové struktury vzájemná vazba.</span><span class="sxs-lookup"><span data-stu-id="c2213-528">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="c2213-529">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="c2213-529">Guidance and conclusions</span></span>

* <span data-ttu-id="c2213-530">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="c2213-530">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="c2213-531">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="c2213-531">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="c2213-532">Nepište dlouhé bloky ručně implementované `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="c2213-532">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="c2213-533">Preferovat `.razor` soubory a umožněte kompilátoru, aby se zabývat pořadovým číslem.</span><span class="sxs-lookup"><span data-stu-id="c2213-533">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="c2213-534">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="c2213-534">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="c2213-535">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="c2213-535">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="c2213-536">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="c2213-536">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="c2213-537">Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="c2213-537">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="c2213-538">Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro `.razor` vývojáře, kteří vytváří soubory.</span><span class="sxs-lookup"><span data-stu-id="c2213-538">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
