---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: blazor/components
ms.openlocfilehash: 123e6e1f798aa5a111bd9eabb492c3e015ae0c5d
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440315"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="26d13-103">Vytváření a používání ASP.NET Corech komponent Razor</span><span class="sxs-lookup"><span data-stu-id="26d13-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="26d13-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="26d13-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="26d13-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26d13-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="26d13-106">Aplikace Blazor jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="26d13-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="26d13-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="26d13-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="26d13-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="26d13-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="26d13-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="26d13-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="26d13-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="26d13-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="26d13-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="26d13-111">Component classes</span></span>

<span data-ttu-id="26d13-112">Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="26d13-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="26d13-113">Komponenta v Blazor je formálně označována jako *Komponenta Razor*.</span><span class="sxs-lookup"><span data-stu-id="26d13-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="26d13-114">Komponenty mohou být vytvořeny pomocí přípony souboru *. cshtml* , pokud jsou soubory označeny jako soubory komponenty Razor pomocí `_RazorComponentInclude` vlastnosti MSBuild.</span><span class="sxs-lookup"><span data-stu-id="26d13-114">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="26d13-115">Například aplikace, která určuje, že všechny soubory *. cshtml* ve složce *Pages* by měly být považovány za soubory součástí Razor:</span><span class="sxs-lookup"><span data-stu-id="26d13-115">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="26d13-116">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="26d13-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="26d13-117">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="26d13-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="26d13-118">Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="26d13-119">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="26d13-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="26d13-120">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="26d13-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="26d13-121">`@code` V bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="26d13-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="26d13-122">Je přípustný více `@code` než jeden blok.</span><span class="sxs-lookup"><span data-stu-id="26d13-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="26d13-123">V předchozích verzích ASP.NET Core `@functions` byly bloky použity pro stejný účel jako `@code` bloky.</span><span class="sxs-lookup"><span data-stu-id="26d13-123">In previous versions of ASP.NET Core, `@functions` blocks were used for the same purpose as `@code` blocks.</span></span> <span data-ttu-id="26d13-124">`@functions`bloky budou i nadále fungovat, ale doporučujeme použít `@code` direktivu.</span><span class="sxs-lookup"><span data-stu-id="26d13-124">`@functions` blocks continue to work, but we recommend using the `@code` directive.</span></span>

<span data-ttu-id="26d13-125">Členy komponenty pak lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají `@`na.</span><span class="sxs-lookup"><span data-stu-id="26d13-125">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="26d13-126">Například C# pole se vykreslí pomocí předpony `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="26d13-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="26d13-127">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="26d13-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="26d13-128">`_headingFontStyle`do hodnoty vlastnosti CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="26d13-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="26d13-129">`_headingText`k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="26d13-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="26d13-130">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="26d13-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="26d13-131">Blazor pak porovná nový strom vykreslování s předchozí a použije všechny změny v model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="26d13-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="26d13-132">Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="26d13-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="26d13-133">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="26d13-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="26d13-134">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="26d13-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="26d13-135">Chcete-li použít vlastní složku, buď přidejte obor názvů vlastní složky do nadřazené komponenty nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="26d13-135">To use a custom folder, either add the custom folder's namespace to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="26d13-136">Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je `WebApplication`kořenový obor názvů aplikace:</span><span class="sxs-lookup"><span data-stu-id="26d13-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="26d13-137">Integrace součástí do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="26d13-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="26d13-138">Použijte komponenty se stávajícími Razor Pages a MVC aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="26d13-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="26d13-139">Aby bylo možné použít součásti Razor, není nutné přepsat existující stránky ani zobrazení.</span><span class="sxs-lookup"><span data-stu-id="26d13-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="26d13-140">Po vykreslení stránky nebo zobrazení jsou komponenty předem vygenerovány ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="26d13-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="26d13-141">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte `RenderComponentAsync<TComponent>` pomocnou metodu HTML:</span><span class="sxs-lookup"><span data-stu-id="26d13-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="26d13-142">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="26d13-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="26d13-143">Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="26d13-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="26d13-144">Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="26d13-145">Další informace o tom, jak se komponenty vykreslují a stav komponenty se spravuje v Blazorch aplikacích na straně serveru, <xref:blazor/hosting-models> najdete v článku.</span><span class="sxs-lookup"><span data-stu-id="26d13-145">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="using-components"></a><span data-ttu-id="26d13-146">Používání komponent</span><span class="sxs-lookup"><span data-stu-id="26d13-146">Using components</span></span>

<span data-ttu-id="26d13-147">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="26d13-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="26d13-148">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="26d13-149">Následující kód v *indexu. Razor* vykreslí `HeadingComponent` instanci:</span><span class="sxs-lookup"><span data-stu-id="26d13-149">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="26d13-150">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26d13-150">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

## <a name="component-parameters"></a><span data-ttu-id="26d13-151">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="26d13-151">Component parameters</span></span>

<span data-ttu-id="26d13-152">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí vlastností (obvykle *neveřejné*) `[Parameter]` třídy komponenty s atributem.</span><span class="sxs-lookup"><span data-stu-id="26d13-152">Components can have *component parameters*, which are defined using properties (usually *non-public*) on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="26d13-153">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="26d13-153">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="26d13-154">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26d13-154">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="26d13-155">V následujícím příkladu `ParentComponent` nastaví hodnotu `Title` vlastnosti `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="26d13-155">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="26d13-156">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26d13-156">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="26d13-157">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="26d13-157">Child content</span></span>

<span data-ttu-id="26d13-158">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-158">Components can set the content of another component.</span></span> <span data-ttu-id="26d13-159">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="26d13-159">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="26d13-160">V následujícím příkladu `ChildComponent` `ChildContent` má vlastnost, která představuje `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="26d13-160">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="26d13-161">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="26d13-161">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="26d13-162">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v `panel-body`panelu Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="26d13-162">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="26d13-163">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26d13-163">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="26d13-164">Vlastnost, která přijímá `RenderFragment` obsah, musí být `ChildContent` pojmenována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="26d13-164">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="26d13-165">Následující `ParentComponent` obsah vám může poskytnout obsah pro `ChildComponent` vykreslování `<ChildComponent>` umístěním obsahu uvnitř značek.</span><span class="sxs-lookup"><span data-stu-id="26d13-165">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="26d13-166">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26d13-166">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="26d13-167">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="26d13-167">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="26d13-168">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="26d13-168">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="26d13-169">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí `@attributes` direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="26d13-169">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the `@attributes` Razor directive.</span></span> <span data-ttu-id="26d13-170">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="26d13-170">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="26d13-171">Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="26d13-171">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="26d13-172">V `<input>` následujícím příkladu první prvek (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="26d13-172">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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
```

<span data-ttu-id="26d13-173">Typ parametru musí být možné přiřadit `Dictionary<string, object>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="26d13-173">The type of the parameter must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="26d13-174">Použití `IEnumerable<KeyValuePair<string, object>>` a`IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="26d13-174">Using `IEnumerable<KeyValuePair<string, object>>` and `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="26d13-175">Vykreslené `<input>` elementy pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="26d13-175">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="26d13-176">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí `[Parameter]` atributu `CaptureUnmatchedAttributes` s vlastností nastavenou na `true`:</span><span class="sxs-lookup"><span data-stu-id="26d13-176">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedAttributes` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedAttributes = true)]
    private Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="26d13-177">`CaptureUnmatchedAttributes` Vlastnost on`[Parameter]` umožňuje, aby tento parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="26d13-177">The `CaptureUnmatchedAttributes` property on `[Parameter]` allows that parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="26d13-178">Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedAttributes`.</span><span class="sxs-lookup"><span data-stu-id="26d13-178">A component can only define a single parameter with `CaptureUnmatchedAttributes`.</span></span>

## <a name="data-binding"></a><span data-ttu-id="26d13-179">Datová vazba</span><span class="sxs-lookup"><span data-stu-id="26d13-179">Data binding</span></span>

<span data-ttu-id="26d13-180">Datové vazby na součásti a elementy modelu DOM jsou provedeny `@bind` atributem.</span><span class="sxs-lookup"><span data-stu-id="26d13-180">Data binding to both components and DOM elements is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="26d13-181">V následujícím příkladu je svázáno `_italicsCheck` pole se zaškrtnutým stavem zaškrtávací políčko:</span><span class="sxs-lookup"><span data-stu-id="26d13-181">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="26d13-182">Když je políčko zaškrtnuté a políčko je zaškrtnuté, hodnota vlastnosti se aktualizuje na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="26d13-182">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="26d13-183">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="26d13-183">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="26d13-184">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se v uživatelském rozhraní obvykle projeví aktualizace vlastností.</span><span class="sxs-lookup"><span data-stu-id="26d13-184">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="26d13-185">Použití `@bind` s`CurrentValue` vlastností(`<input @bind="CurrentValue" />`) je v podstatě ekvivalentem následujícího:</span><span class="sxs-lookup"><span data-stu-id="26d13-185">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="26d13-186">Při vykreslení `value` komponenty `CurrentValue` z vlastnosti input element přichází.</span><span class="sxs-lookup"><span data-stu-id="26d13-186">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="26d13-187">Když uživatel zadá do textového pole, `onchange` událost se aktivuje `CurrentValue` a vlastnost je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="26d13-187">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="26d13-188">Ve skutečnosti je generování kódu trochu složitější, protože `@bind` zpracovává několik případů, kde jsou prováděny převody typu.</span><span class="sxs-lookup"><span data-stu-id="26d13-188">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="26d13-189">V zásadě `@bind` přidruží aktuální hodnotu výrazu `value` k atributu a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="26d13-189">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="26d13-190">Kromě zpracování `onchange` událostí pomocí `@bind` syntaxe lze vlastnost nebo pole svázat pomocí `@bind-value` jiných událostí `event` zadáním atributu s parametrem.</span><span class="sxs-lookup"><span data-stu-id="26d13-190">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an `@bind-value` attribute with an `event` parameter.</span></span> <span data-ttu-id="26d13-191">Následující příklad váže `CurrentValue` vlastnost `oninput` pro událost:</span><span class="sxs-lookup"><span data-stu-id="26d13-191">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="26d13-192">Na rozdíl `onchange`od, která je aktivována, když prvek ztratí `oninput` fokus, je aktivována při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="26d13-192">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="26d13-193">**Řetězce formátu**</span><span class="sxs-lookup"><span data-stu-id="26d13-193">**Format strings**</span></span>

<span data-ttu-id="26d13-194">Datové vazby fungují s <xref:System.DateTime> formátovacími řetězci.</span><span class="sxs-lookup"><span data-stu-id="26d13-194">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="26d13-195">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="26d13-195">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="26d13-196">Atribut určuje formát data, který se má použít `value` pro `<input>` element. `@bind:format`</span><span class="sxs-lookup"><span data-stu-id="26d13-196">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="26d13-197">Formát je také použit k analýze hodnoty při `onchange` výskytu události.</span><span class="sxs-lookup"><span data-stu-id="26d13-197">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="26d13-198">**Parametry součásti**</span><span class="sxs-lookup"><span data-stu-id="26d13-198">**Component parameters**</span></span>

<span data-ttu-id="26d13-199">Vazba také rozpoznává parametry komponenty, `@bind-{property}` kde může svázat hodnotu vlastnosti napříč komponentami.</span><span class="sxs-lookup"><span data-stu-id="26d13-199">Binding also recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="26d13-200">Následující podřízená komponenta (`ChildComponent`) `Year` má parametr komponenty a `YearChanged` zpětné volání:</span><span class="sxs-lookup"><span data-stu-id="26d13-200">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="26d13-201">`EventCallback<T>`je vysvětleno v části [vnořenou eventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="26d13-201">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="26d13-202">Následující nadřazená komponenta používá `ChildComponent` a váže `ParentYear` parametr `Year` z nadřazené položky k parametru v podřízené komponentě:</span><span class="sxs-lookup"><span data-stu-id="26d13-202">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="26d13-203">Načítání kódu `ParentComponent` generuje následující značky:</span><span class="sxs-lookup"><span data-stu-id="26d13-203">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="26d13-204">Pokud je hodnota `ParentYear` vlastnosti změněna výběrem tlačítka `ParentComponent`v `ChildComponent` , `Year` vlastnost je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="26d13-204">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="26d13-205">Nová hodnota `Year` se vykreslí v uživatelském rozhraní, `ParentComponent` když se znovu vykreslí:</span><span class="sxs-lookup"><span data-stu-id="26d13-205">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="26d13-206">Parametr je svázán, protože má doprovodnou `YearChanged` událost, která `Year` odpovídá typu parametru. `Year`</span><span class="sxs-lookup"><span data-stu-id="26d13-206">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="26d13-207">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` má v podstatě ekvivalent zápisu:</span><span class="sxs-lookup"><span data-stu-id="26d13-207">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="26d13-208">Obecně platí, že vlastnost může být svázána s odpovídající obslužnou rutinou `@bind-property:event` události pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="26d13-208">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="26d13-209">Vlastnost `MyProp` může být například svázána s `MyEventHandler` použitím následujících dvou atributů:</span><span class="sxs-lookup"><span data-stu-id="26d13-209">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="26d13-210">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="26d13-210">Event handling</span></span>

<span data-ttu-id="26d13-211">Komponenty Razor poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="26d13-211">Razor components provide event handling features.</span></span> <span data-ttu-id="26d13-212">Pro atribut elementu HTML s názvem `on<event>` ( `onclick` například a `onsubmit`) s hodnotou typu delegáta, komponenty Razor považují hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="26d13-212">For an HTML element attribute named `on<event>` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="26d13-213">Název atributu vždy začíná `@on`na.</span><span class="sxs-lookup"><span data-stu-id="26d13-213">The attribute's name always starts with `@on`.</span></span>

<span data-ttu-id="26d13-214">Následující kód volá `UpdateHeading` metodu, pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="26d13-214">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="26d13-215">Následující kód volá `CheckChanged` metodu, když je zaškrtávací políčko změněno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="26d13-215">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckboxChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="26d13-216">Obslužné rutiny událostí mohou být také asynchronní a <xref:System.Threading.Tasks.Task>vracet.</span><span class="sxs-lookup"><span data-stu-id="26d13-216">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="26d13-217">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="26d13-217">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="26d13-218">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="26d13-218">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="26d13-219">V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="26d13-219">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

<span data-ttu-id="26d13-220">U některých událostí jsou povoleny typy argumentů události specifické pro událost.</span><span class="sxs-lookup"><span data-stu-id="26d13-220">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="26d13-221">Pokud přístup k některému z těchto typů událostí není nezbytný, není nutné ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="26d13-221">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="26d13-222">Podporované [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="26d13-222">Supported [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) are shown in the following table.</span></span>

| <span data-ttu-id="26d13-223">Událost</span><span class="sxs-lookup"><span data-stu-id="26d13-223">Event</span></span> | <span data-ttu-id="26d13-224">Třída</span><span class="sxs-lookup"><span data-stu-id="26d13-224">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="26d13-225">Schránka</span><span class="sxs-lookup"><span data-stu-id="26d13-225">Clipboard</span></span> | `UIClipboardEventArgs` |
| <span data-ttu-id="26d13-226">Přetažení</span><span class="sxs-lookup"><span data-stu-id="26d13-226">Drag</span></span>  | <span data-ttu-id="26d13-227">`UIDragEventArgs`slouží k uchovávání přetažených dat během operace přetažení a může obsahovat jeden nebo více `UIDataTransferItem`. &ndash; `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="26d13-227">`UIDragEventArgs` &ndash; `DataTransfer` is used to hold the dragged data during a drag and drop operation and may hold one or more `UIDataTransferItem`.</span></span> <span data-ttu-id="26d13-228">`UIDataTransferItem`reprezentuje jednu položku dat přetažením.</span><span class="sxs-lookup"><span data-stu-id="26d13-228">`UIDataTransferItem` represents one drag data item.</span></span> |
| <span data-ttu-id="26d13-229">Chyba</span><span class="sxs-lookup"><span data-stu-id="26d13-229">Error</span></span> | `UIErrorEventArgs` |
| <span data-ttu-id="26d13-230">Vybrána</span><span class="sxs-lookup"><span data-stu-id="26d13-230">Focus</span></span> | <span data-ttu-id="26d13-231">`UIFocusEventArgs`Nezahrnuje podporu pro `relatedTarget`. &ndash;</span><span class="sxs-lookup"><span data-stu-id="26d13-231">`UIFocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="26d13-232">`<input>`mění</span><span class="sxs-lookup"><span data-stu-id="26d13-232">`<input>` change</span></span> | `UIChangeEventArgs` |
| <span data-ttu-id="26d13-233">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="26d13-233">Keyboard</span></span> | `UIKeyboardEventArgs` |
| <span data-ttu-id="26d13-234">Stisknut</span><span class="sxs-lookup"><span data-stu-id="26d13-234">Mouse</span></span> | `UIMouseEventArgs` |
| <span data-ttu-id="26d13-235">Ukazatele myši</span><span class="sxs-lookup"><span data-stu-id="26d13-235">Mouse pointer</span></span> | `UIPointerEventArgs` |
| <span data-ttu-id="26d13-236">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="26d13-236">Mouse wheel</span></span> | `UIWheelEventArgs` |
| <span data-ttu-id="26d13-237">Průběh</span><span class="sxs-lookup"><span data-stu-id="26d13-237">Progress</span></span> | `UIProgressEventArgs` |
| <span data-ttu-id="26d13-238">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="26d13-238">Touch</span></span> | <span data-ttu-id="26d13-239">`UITouchEventArgs`&ndash; představujejedenkontaktníbodnazařízenícitlivém`UITouchPoint` na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="26d13-239">`UITouchEventArgs` &ndash; `UITouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="26d13-240">Informace o vlastnostech a chování zpracování událostí událostí v předchozí tabulce naleznete v tématu [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) ve zdroji odkazu.</span><span class="sxs-lookup"><span data-stu-id="26d13-240">For information on the properties and event handling behavior of the events in the preceding table, see [UIEventArgs](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/UIEventArgs.cs) in the reference source.</span></span>
  
<span data-ttu-id="26d13-241">Lambda výrazy lze také použít:</span><span class="sxs-lookup"><span data-stu-id="26d13-241">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="26d13-242">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="26d13-242">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="26d13-243">Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a jeho čísla tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="26d13-243">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="26d13-244">Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="26d13-244">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="26d13-245">V opačném případě se stejná proměnná používá ve všech výrazech lambda, což způsobuje `i`, že hodnota je stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="26d13-245">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="26d13-246">Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="26d13-246">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="26d13-247">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="26d13-247">EventCallback</span></span>

<span data-ttu-id="26d13-248">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde&mdash;k události podřízené komponenty, například když dojde k události v podřízeném objektu. `onclick`</span><span class="sxs-lookup"><span data-stu-id="26d13-248">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="26d13-249">Chcete-li zobrazit události napříč komponentami `EventCallback`, použijte.</span><span class="sxs-lookup"><span data-stu-id="26d13-249">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="26d13-250">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="26d13-250">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="26d13-251">V ukázkové aplikaci ukazuje, jak `EventCallback` je nastavena `onclick` obslužná rutina tlačítka pro příjem delegáta z ukázky `ParentComponent`. `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="26d13-251">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="26d13-252">Je zadaný s `UIMouseEventArgs` ,`onclick` který je vhodný pro událost z periferního zařízení: `EventCallback`</span><span class="sxs-lookup"><span data-stu-id="26d13-252">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="26d13-253">Nastaví podřízený objekt `ShowMessage`najehometodu: `EventCallback<T>` `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="26d13-253">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="26d13-254">Když je vybráno tlačítko v `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="26d13-254">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="26d13-255">`ParentComponent`Je volána metoda.`ShowMessage`</span><span class="sxs-lookup"><span data-stu-id="26d13-255">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="26d13-256">`messageText`se aktualizuje a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="26d13-256">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="26d13-257">Volání `StateHasChanged` není vyžadováno v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="26d13-257">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="26d13-258">`StateHasChanged`je volána automaticky pro `ParentComponent`revykreslování, stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="26d13-258">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="26d13-259">`EventCallback`a `EventCallback<T>` povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="26d13-259">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="26d13-260">`EventCallback<T>`je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="26d13-260">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="26d13-261">`EventCallback`je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="26d13-261">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="26d13-262">`EventCallback` Vyvolat nebo `EventCallback<T>` s a`InvokeAsync` očekávat :<xref:System.Threading.Tasks.Task></span><span class="sxs-lookup"><span data-stu-id="26d13-262">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="26d13-263">Použití `EventCallback` a`EventCallback<T>` pro zpracování událostí a parametry komponenty vazby.</span><span class="sxs-lookup"><span data-stu-id="26d13-263">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="26d13-264">Preferovat silného typu `EventCallback<T>` přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="26d13-264">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="26d13-265">`EventCallback<T>`poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-265">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="26d13-266">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="26d13-266">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="26d13-267">Použijte `EventCallback` v případě, že zpětnému volání není předáno žádné číslo.</span><span class="sxs-lookup"><span data-stu-id="26d13-267">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="26d13-268">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="26d13-268">Capture references to components</span></span>

<span data-ttu-id="26d13-269">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="26d13-269">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="26d13-270">Chcete-li zachytit odkaz na komponentu, `@ref` přidejte atribut do podřízené komponenty a pak definujte pole se stejným názvem a stejným typem jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="26d13-270">To capture a component reference, add a `@ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

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

<span data-ttu-id="26d13-271">Při vykreslení `loginDialog` komponenty je pole vyplněno `MyLoginDialog` instancí podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-271">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="26d13-272">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-272">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="26d13-273">Proměnná je naplněna pouze po vykreslení komponenty a její výstup `MyLoginDialog` obsahuje element. `loginDialog`</span><span class="sxs-lookup"><span data-stu-id="26d13-273">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="26d13-274">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="26d13-274">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="26d13-275">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte `OnAfterRenderAsync` metody `OnAfterRender` nebo.</span><span class="sxs-lookup"><span data-stu-id="26d13-275">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="26d13-276">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není to funkce [interoperability JavaScriptu](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="26d13-276">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="26d13-277">Odkazy na součásti nejsou předány kódu&mdash;jazyka JavaScript, které jsou používány pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="26d13-277">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="26d13-278">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="26d13-278">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="26d13-279">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="26d13-279">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="26d13-280">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="26d13-280">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="26d13-281">Slouží @key k řízení uchovávání prvků a komponent.</span><span class="sxs-lookup"><span data-stu-id="26d13-281">Use @key to control the preservation of elements and components</span></span>

<span data-ttu-id="26d13-282">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazor rozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="26d13-282">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="26d13-283">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="26d13-283">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="26d13-284">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="26d13-284">Consider the following example:</span></span>

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

<span data-ttu-id="26d13-285">Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="26d13-285">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="26d13-286">Když se komponenta znovu vykreslí, může `<DetailsEditor>` se tato součást změnit, aby `Details` přijímala jiné hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="26d13-286">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="26d13-287">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="26d13-287">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="26d13-288">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="26d13-288">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="26d13-289">Proces mapování lze řídit pomocí `@key` atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="26d13-289">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="26d13-290">`@key`způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="26d13-290">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="26d13-291">Když se `People` kolekce změní, rozdílový algoritmus zachovává přidružení mezi `<DetailsEditor>` instancemi a `person` instancemi:</span><span class="sxs-lookup"><span data-stu-id="26d13-291">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="26d13-292">Pokud se ze seznamu odstraní, z uživatelského rozhraní se odebere `<DetailsEditor>` jenom odpovídající instance. `People` `Person`</span><span class="sxs-lookup"><span data-stu-id="26d13-292">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="26d13-293">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="26d13-293">Other instances are left unchanged.</span></span>
* <span data-ttu-id="26d13-294">Pokud je vložena na nějaké místo v seznamu, je do příslušné `<DetailsEditor>` pozice vložena jedna nová instance. `Person`</span><span class="sxs-lookup"><span data-stu-id="26d13-294">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="26d13-295">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="26d13-295">Other instances are left unchanged.</span></span>
* <span data-ttu-id="26d13-296">Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="26d13-296">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="26d13-297">V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="26d13-297">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="26d13-298">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="26d13-298">Keys are local to each container element or component.</span></span> <span data-ttu-id="26d13-299">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="26d13-299">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="26d13-300">Kdy použít@key</span><span class="sxs-lookup"><span data-stu-id="26d13-300">When to use @key</span></span>

<span data-ttu-id="26d13-301">Obvykle má smysl použít `@key` při každém vykreslení seznamu (například `@foreach` v bloku) a existuje vhodná `@key`hodnota pro definování.</span><span class="sxs-lookup"><span data-stu-id="26d13-301">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="26d13-302">Můžete také použít `@key` , chcete-li zabránit Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="26d13-302">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="26d13-303">Pokud `@currentPerson` se změní `@key` , direktiva Attribute vynutí, aby Blazor `<div>` zahození celého a jeho následníků a znovu sestavil podstrom v uživatelském rozhraní s novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="26d13-303">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="26d13-304">To může být užitečné, pokud potřebujete zaručit, že se při `@currentPerson` změnách nezachovají stav uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="26d13-304">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="26d13-305">Kdy se nepoužívá@key</span><span class="sxs-lookup"><span data-stu-id="26d13-305">When not to use @key</span></span>

<span data-ttu-id="26d13-306">Při rozdílech s se `@key`účtují náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="26d13-306">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="26d13-307">Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="26d13-307">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="26d13-308">`@key` I když se nepoužívá, Blazor zachová podřízený element a instance komponenty co nejvíc.</span><span class="sxs-lookup"><span data-stu-id="26d13-308">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="26d13-309">Jedinou výhodou použití `@key` je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="26d13-309">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="26d13-310">Jaké hodnoty se mají použít pro@key</span><span class="sxs-lookup"><span data-stu-id="26d13-310">What values to use for @key</span></span>

<span data-ttu-id="26d13-311">Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro `@key`:</span><span class="sxs-lookup"><span data-stu-id="26d13-311">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="26d13-312">Instance objektů modelu (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="26d13-312">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="26d13-313">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="26d13-313">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="26d13-314">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="26d13-314">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="26d13-315">Vyhněte se zadávání hodnoty, která může kolidovat neočekávaně.</span><span class="sxs-lookup"><span data-stu-id="26d13-315">Avoid supplying a value that can clash unexpectedly.</span></span> <span data-ttu-id="26d13-316">Pokud `@key="@someObject.GetHashCode()"` je zadaný, může dojít k neočekávaným konfliktům, protože kódy hash nesouvisejících objektů můžou být stejné.</span><span class="sxs-lookup"><span data-stu-id="26d13-316">If `@key="@someObject.GetHashCode()"` is supplied, unexpected clashes may occur because the hash codes of unrelated objects can be the same.</span></span> <span data-ttu-id="26d13-317">Pokud jsou `@key` hodnoty v konfliktu požadovány v rámci stejné nadřazené položky `@key` , hodnoty nebudou přijaty.</span><span class="sxs-lookup"><span data-stu-id="26d13-317">If clashing `@key` values are requested within the same parent, the `@key` values won't be honored.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="26d13-318">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="26d13-318">Lifecycle methods</span></span>

<span data-ttu-id="26d13-319">`OnInitAsync`a `OnInit` spusťte kód pro inicializaci komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-319">`OnInitAsync` and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="26d13-320">Chcete-li provést asynchronní operaci, `OnInitAsync` použijte `await` a klíčové slovo pro operaci:</span><span class="sxs-lookup"><span data-stu-id="26d13-320">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="26d13-321">Pro synchronní operaci použijte `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="26d13-321">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

<span data-ttu-id="26d13-322">`OnParametersSetAsync`a `OnParametersSet` jsou volány, když komponenta přijímá parametry z nadřazené položky a hodnoty jsou přiřazeny vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="26d13-322">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="26d13-323">Tyto metody jsou spouštěny po inicializaci komponenty a pokaždé, když je vykreslena komponenta:</span><span class="sxs-lookup"><span data-stu-id="26d13-323">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="26d13-324">`OnAfterRenderAsync`a `OnAfterRender` jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-324">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="26d13-325">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="26d13-325">Element and component references are populated at this point.</span></span> <span data-ttu-id="26d13-326">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="26d13-326">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

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

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="26d13-327">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="26d13-327">Handle incomplete async actions at render</span></span>

<span data-ttu-id="26d13-328">Asynchronní akce provedené v událostech životního cyklu se možná nedokončily, než se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="26d13-328">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="26d13-329">Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty.</span><span class="sxs-lookup"><span data-stu-id="26d13-329">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="26d13-330">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="26d13-330">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="26d13-331">Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), `null`zatímco objekty jsou.</span><span class="sxs-lookup"><span data-stu-id="26d13-331">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="26d13-332">V komponentě `OnInitAsync` šablon Blazor je přepsáno na asynchronně příjem dat předpovědi (`forecasts`). `FetchData`</span><span class="sxs-lookup"><span data-stu-id="26d13-332">In the `FetchData` component of the Blazor templates, `OnInitAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="26d13-333">V takovém případě `forecasts` se uživateli zobrazí zpráva o načítání. `null`</span><span class="sxs-lookup"><span data-stu-id="26d13-333">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="26d13-334">`Task` Po úspěšnémdokončenísekomponentaznovuvykreslís`OnInitAsync` aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="26d13-334">After the `Task` returned by `OnInitAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="26d13-335">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26d13-335">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="26d13-336">Spustit kód před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="26d13-336">Execute code before parameters are set</span></span>

<span data-ttu-id="26d13-337">`SetParameters`lze přepsat pro spuštění kódu před nastavením parametrů:</span><span class="sxs-lookup"><span data-stu-id="26d13-337">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="26d13-338">Pokud `base.SetParameters` není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="26d13-338">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="26d13-339">Například příchozí parametry nemusejí být přiřazeny vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="26d13-339">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="26d13-340">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="26d13-340">Suppress refreshing of the UI</span></span>

<span data-ttu-id="26d13-341">`ShouldRender`lze přepsat pro potlačení aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="26d13-341">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="26d13-342">Pokud se implementace vrátí `true`, uživatelské rozhraní se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="26d13-342">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="26d13-343">I když `ShouldRender` je přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="26d13-343">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="26d13-344">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="26d13-344">Component disposal with IDisposable</span></span>

<span data-ttu-id="26d13-345">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="26d13-345">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="26d13-346">Následující komponenta používá `@implements IDisposable` `Dispose` a metodu:</span><span class="sxs-lookup"><span data-stu-id="26d13-346">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="26d13-347">Směrování</span><span class="sxs-lookup"><span data-stu-id="26d13-347">Routing</span></span>

<span data-ttu-id="26d13-348">Směrování v Blazor se dosahuje tak, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="26d13-348">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="26d13-349">Když je zkompilován soubor Razor s `@page` direktivou, vygenerovaná třída je <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> dána zadáním šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="26d13-349">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="26d13-350">V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="26d13-350">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="26d13-351">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="26d13-351">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="26d13-352">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="26d13-352">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="26d13-353">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="26d13-353">Route parameters</span></span>

<span data-ttu-id="26d13-354">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v `@page` direktivě.</span><span class="sxs-lookup"><span data-stu-id="26d13-354">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="26d13-355">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="26d13-355">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="26d13-356">*Komponenta parametru směrování*:</span><span class="sxs-lookup"><span data-stu-id="26d13-356">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="26d13-357">Volitelné parametry nejsou podporované, takže se `@page` v předchozím příkladu použijí dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="26d13-357">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="26d13-358">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="26d13-358">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="26d13-359">Druhá `@page` direktiva `{text}` převezme parametr Route a `Text` přiřadí hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="26d13-359">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="26d13-360">Dědičnost základní třídy pro prostředí s kódem na pozadí</span><span class="sxs-lookup"><span data-stu-id="26d13-360">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="26d13-361">Soubory součástí kombinují značky HTML C# a zpracovávají kód ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="26d13-361">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="26d13-362">`@inherits` Direktiva se dá použít k poskytování aplikací Blazor s prostředím "kódu na pozadí", které odděluje označení komponenty od zpracování kódu.</span><span class="sxs-lookup"><span data-stu-id="26d13-362">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="26d13-363">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak může komponenta zdědit základní třídu, `BlazorRocksBase`k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-363">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="26d13-364">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="26d13-364">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="26d13-365">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="26d13-365">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="26d13-366">Základní třída by měla být odvozena od `ComponentBase`třídy.</span><span class="sxs-lookup"><span data-stu-id="26d13-366">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="26d13-367">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="26d13-367">Import components</span></span>

<span data-ttu-id="26d13-368">Obor názvů komponenty vytvořené pomocí Razor je založen na:</span><span class="sxs-lookup"><span data-stu-id="26d13-368">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="26d13-369">Projekt `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="26d13-369">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="26d13-370">Cesta z kořenového adresáře projektu k součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-370">The path from the project root to the component.</span></span> <span data-ttu-id="26d13-371">Například `ComponentsSample/Pages/Index.razor` je v oboru názvů `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="26d13-371">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="26d13-372">Komponenty následují C# pravidla vazeb názvů.</span><span class="sxs-lookup"><span data-stu-id="26d13-372">Components follow C# name binding rules.</span></span> <span data-ttu-id="26d13-373">V případě *indexu. Razor*jsou v oboru *ComponentsSample*všechny komponenty ve stejné složce, *stránkách*a nadřazené složce.</span><span class="sxs-lookup"><span data-stu-id="26d13-373">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="26d13-374">Komponenty definované v jiném oboru názvů lze převést do rozsahu pomocí direktivy [ \@using](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="26d13-374">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="26d13-375">Pokud ve složce `ComponentsSample/Shared/`existuje `NavMenu.razor`jiná součást, lze komponentu použít v `Index.razor` příkazu s následujícím `@using` příkazem:</span><span class="sxs-lookup"><span data-stu-id="26d13-375">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="26d13-376">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které odstraňují nutnost [ \@](xref:mvc/views/razor#using) direktivy using:</span><span class="sxs-lookup"><span data-stu-id="26d13-376">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="26d13-377">`global::` Kvalifikace není podporovaná.</span><span class="sxs-lookup"><span data-stu-id="26d13-377">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="26d13-378">Import komponent s `using` příkazy s aliasem ( `@using Foo = Bar`například) není podporován.</span><span class="sxs-lookup"><span data-stu-id="26d13-378">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="26d13-379">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="26d13-379">Partially qualified names aren't supported.</span></span> <span data-ttu-id="26d13-380">Například přidání `@using ComponentsSample` a `NavMenu.razor` odkazování pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="26d13-380">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="razor-support"></a><span data-ttu-id="26d13-381">Podpora Razor</span><span class="sxs-lookup"><span data-stu-id="26d13-381">Razor support</span></span>

<span data-ttu-id="26d13-382">**Direktivy Razor**</span><span class="sxs-lookup"><span data-stu-id="26d13-382">**Razor directives**</span></span>

<span data-ttu-id="26d13-383">Direktivy Razor jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="26d13-383">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="26d13-384">– Direktiva</span><span class="sxs-lookup"><span data-stu-id="26d13-384">Directive</span></span> | <span data-ttu-id="26d13-385">Popis</span><span class="sxs-lookup"><span data-stu-id="26d13-385">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="26d13-386">\@code</span><span class="sxs-lookup"><span data-stu-id="26d13-386">\@code</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="26d13-387">Přidá blok C# kódu do komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-387">Adds a C# code block to a component.</span></span> <span data-ttu-id="26d13-388">`@code`je alias `@functions`.</span><span class="sxs-lookup"><span data-stu-id="26d13-388">`@code` is an alias of `@functions`.</span></span> <span data-ttu-id="26d13-389">`@code`se doporučuje `@functions`.</span><span class="sxs-lookup"><span data-stu-id="26d13-389">`@code` is recommended over `@functions`.</span></span> <span data-ttu-id="26d13-390">Je přípustný více `@code` než jeden blok.</span><span class="sxs-lookup"><span data-stu-id="26d13-390">More than one `@code` block is permissible.</span></span> |
| [<span data-ttu-id="26d13-391">\@POZVYHLEDAT</span><span class="sxs-lookup"><span data-stu-id="26d13-391">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="26d13-392">Přidá blok C# kódu do komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-392">Adds a C# code block to a component.</span></span> <span data-ttu-id="26d13-393">Vyberte `@code` možnost `@functions` přes C# pro bloky kódu.</span><span class="sxs-lookup"><span data-stu-id="26d13-393">Choose `@code` over `@functions` for C# code blocks.</span></span> |
| `@implements` | <span data-ttu-id="26d13-394">Implementuje rozhraní pro generovanou třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-394">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="26d13-395">\@zdědí</span><span class="sxs-lookup"><span data-stu-id="26d13-395">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="26d13-396">Poskytuje úplnou kontrolu nad třídou, kterou komponenta dědí.</span><span class="sxs-lookup"><span data-stu-id="26d13-396">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="26d13-397">\@vstřik</span><span class="sxs-lookup"><span data-stu-id="26d13-397">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="26d13-398">Povoluje vkládání služby z [kontejneru služby](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="26d13-398">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="26d13-399">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="26d13-399">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="26d13-400">Určuje komponentu rozložení.</span><span class="sxs-lookup"><span data-stu-id="26d13-400">Specifies a layout component.</span></span> <span data-ttu-id="26d13-401">Komponenty rozložení se používají k zamezení Duplikace kódu a nekonzistenci.</span><span class="sxs-lookup"><span data-stu-id="26d13-401">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="26d13-402">\@Page</span><span class="sxs-lookup"><span data-stu-id="26d13-402">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="26d13-403">Určuje, že komponenta má zpracovávat požadavky přímo.</span><span class="sxs-lookup"><span data-stu-id="26d13-403">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="26d13-404">`@page` Direktivu lze zadat pomocí trasy a nepovinných parametrů.</span><span class="sxs-lookup"><span data-stu-id="26d13-404">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="26d13-405">Na `@page` rozdíl od Razor Pages direktiva nemusí být první direktivou v horní části souboru.</span><span class="sxs-lookup"><span data-stu-id="26d13-405">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="26d13-406">Další informace najdete v tématu [Směrování](xref:blazor/routing).</span><span class="sxs-lookup"><span data-stu-id="26d13-406">For more information, see [Routing](xref:blazor/routing).</span></span> |
| [<span data-ttu-id="26d13-407">\@použití</span><span class="sxs-lookup"><span data-stu-id="26d13-407">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="26d13-408">C# Přidádirektivudogenerovanétřídykomponent.`using`</span><span class="sxs-lookup"><span data-stu-id="26d13-408">Adds the C# `using` directive to the generated component class.</span></span> <span data-ttu-id="26d13-409">Tím se také přiřadí všechny součásti definované v tomto oboru názvů do rozsahu.</span><span class="sxs-lookup"><span data-stu-id="26d13-409">This also brings all the components defined in that namespace into scope.</span></span> |
| [<span data-ttu-id="26d13-410">\@namespace</span><span class="sxs-lookup"><span data-stu-id="26d13-410">\@namespace</span></span>](xref:mvc/views/razor#section-6) | <span data-ttu-id="26d13-411">Nastaví obor názvů generované třídy součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-411">Sets the namespace of the generated component class.</span></span> |
| [<span data-ttu-id="26d13-412">\@přidělen</span><span class="sxs-lookup"><span data-stu-id="26d13-412">\@attribute</span></span>](xref:mvc/views/razor#section-7) | <span data-ttu-id="26d13-413">Přidá atribut do generované třídy komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-413">Adds an attribute to the generated component class.</span></span> |

<span data-ttu-id="26d13-414">**Podmíněné atributy elementu HTML**</span><span class="sxs-lookup"><span data-stu-id="26d13-414">**Conditional HTML element attributes**</span></span>

<span data-ttu-id="26d13-415">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="26d13-415">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="26d13-416">Pokud je `false` hodnota nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="26d13-416">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="26d13-417">Pokud je `true`hodnota, je vygenerována hodnota atributu minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="26d13-417">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="26d13-418">V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="26d13-418">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="26d13-419">Pokud `IsCompleted` je`true`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="26d13-419">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="26d13-420">Pokud `IsCompleted` je`false`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="26d13-420">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="26d13-421">**Další informace o Razor**</span><span class="sxs-lookup"><span data-stu-id="26d13-421">**Additional information on Razor**</span></span>

<span data-ttu-id="26d13-422">Další informace o Razor najdete v referenčních informacích o [syntaxe Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="26d13-422">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="26d13-423">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="26d13-423">Raw HTML</span></span>

<span data-ttu-id="26d13-424">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="26d13-424">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="26d13-425">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="26d13-425">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="26d13-426">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="26d13-426">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="26d13-427">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="26d13-427">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="26d13-428">Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="26d13-428">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="26d13-429">Komponenty se šablonami</span><span class="sxs-lookup"><span data-stu-id="26d13-429">Templated components</span></span>

<span data-ttu-id="26d13-430">Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-430">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="26d13-431">Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-431">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="26d13-432">Mezi několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="26d13-432">A couple of examples include:</span></span>

* <span data-ttu-id="26d13-433">Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="26d13-433">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="26d13-434">Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="26d13-434">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="26d13-435">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="26d13-435">Template parameters</span></span>

<span data-ttu-id="26d13-436">Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo. `RenderFragment<T>`</span><span class="sxs-lookup"><span data-stu-id="26d13-436">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="26d13-437">Fragment vykreslování představuje segment uživatelského rozhraní, který je vykreslen komponentou.</span><span class="sxs-lookup"><span data-stu-id="26d13-437">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="26d13-438">Fragment vykreslování volitelně převezme parametr, který lze zadat při vyvolání fragmentu vykreslování.</span><span class="sxs-lookup"><span data-stu-id="26d13-438">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="26d13-439">`TableTemplate`část</span><span class="sxs-lookup"><span data-stu-id="26d13-439">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="26d13-440">Při použití šablonované komponenty lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="26d13-440">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="26d13-441">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="26d13-441">Template context parameters</span></span>

<span data-ttu-id="26d13-442">Argumenty součásti typu `RenderFragment<T>` předané jako elementy mají implicitní parametr s názvem `context` (například z předchozího příkladu `@context.PetId`kódu), ale můžete změnit název parametru pomocí `Context` atributu u podřízeného objektu. objekt.</span><span class="sxs-lookup"><span data-stu-id="26d13-442">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="26d13-443">V následujícím příkladu `RowTemplate` `Context` atribut prvku Určuje `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="26d13-443">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="26d13-444">Alternativně lze zadat `Context` atribut prvku komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-444">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="26d13-445">Zadaný `Context` atribut se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="26d13-445">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="26d13-446">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu).</span><span class="sxs-lookup"><span data-stu-id="26d13-446">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="26d13-447">V následujícím příkladu `Context` se atribut zobrazí `TableTemplate` na elementu a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="26d13-447">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="26d13-448">Komponenty s obecným typem</span><span class="sxs-lookup"><span data-stu-id="26d13-448">Generic-typed components</span></span>

<span data-ttu-id="26d13-449">Komponenty se šablonami jsou často typu obecně typované.</span><span class="sxs-lookup"><span data-stu-id="26d13-449">Templated components are often generically typed.</span></span> <span data-ttu-id="26d13-450">Například obecná `ListViewTemplate` komponenta může být použita k vykreslování `IEnumerable<T>` hodnot.</span><span class="sxs-lookup"><span data-stu-id="26d13-450">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="26d13-451">Chcete-li definovat obecné komponenty, použijte `@typeparam` direktivu k určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="26d13-451">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="26d13-452">Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="26d13-452">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="26d13-453">V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="26d13-453">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="26d13-454">V následujícím příkladu `TItem="Pet"` určuje typ:</span><span class="sxs-lookup"><span data-stu-id="26d13-454">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="26d13-455">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="26d13-455">Cascading values and parameters</span></span>

<span data-ttu-id="26d13-456">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="26d13-456">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="26d13-457">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="26d13-457">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="26d13-458">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="26d13-458">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="26d13-459">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="26d13-459">Theme example</span></span>

<span data-ttu-id="26d13-460">V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="26d13-460">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="26d13-461">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="26d13-461">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="26d13-462">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="26d13-462">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="26d13-463">`CascadingValue` Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="26d13-463">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="26d13-464">Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="26d13-464">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="26d13-465">`ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="26d13-465">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="26d13-466">Každá odvozená komponenta může tuto vlastnost spotřebovat `ThemeInfo` prostřednictvím kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="26d13-466">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="26d13-467">`CascadingValuesParametersLayout`část</span><span class="sxs-lookup"><span data-stu-id="26d13-467">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="26d13-468">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí `[CascadingParameter]` atributu nebo na základě hodnoty řetězcového názvu:</span><span class="sxs-lookup"><span data-stu-id="26d13-468">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="26d13-469">Vazba s hodnotou názvu řetězce je relevantní, pokud máte více kaskádových hodnot stejného typu a potřebujete je odlišit v rámci stejného podstromu.</span><span class="sxs-lookup"><span data-stu-id="26d13-469">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="26d13-470">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="26d13-470">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="26d13-471">V ukázkové aplikaci `CascadingValuesParametersTheme` váže `ThemeInfo` komponenta kaskádovou hodnotu k kaskádovým parametrům.</span><span class="sxs-lookup"><span data-stu-id="26d13-471">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="26d13-472">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="26d13-472">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="26d13-473">`CascadingValuesParametersTheme`část</span><span class="sxs-lookup"><span data-stu-id="26d13-473">`CascadingValuesParametersTheme` component:</span></span>

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

### <a name="tabset-example"></a><span data-ttu-id="26d13-474">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="26d13-474">TabSet example</span></span>

<span data-ttu-id="26d13-475">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="26d13-475">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="26d13-476">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="26d13-476">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="26d13-477">Ukázková aplikace má `ITab` rozhraní, které implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="26d13-477">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="26d13-478">Komponenta používá komponentu, která obsahuje několik `Tab` komponent: `TabSet` `CascadingValuesParametersTabSet`</span><span class="sxs-lookup"><span data-stu-id="26d13-478">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="26d13-479">Podřízené `Tab` součásti nejsou explicitně předány jako parametry `TabSet`do.</span><span class="sxs-lookup"><span data-stu-id="26d13-479">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="26d13-480">Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu. `TabSet`</span><span class="sxs-lookup"><span data-stu-id="26d13-480">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="26d13-481">Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. `TabSet` Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu `TabSet` , komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta `Tab` podřízenými součástmi.</span><span class="sxs-lookup"><span data-stu-id="26d13-481">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="26d13-482">`TabSet`část</span><span class="sxs-lookup"><span data-stu-id="26d13-482">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="26d13-483">Podřízené komponenty `Tab` zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do `TabSet` souřadnice a, na které karty jsou aktivní.</span><span class="sxs-lookup"><span data-stu-id="26d13-483">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="26d13-484">`Tab`část</span><span class="sxs-lookup"><span data-stu-id="26d13-484">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="26d13-485">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="26d13-485">Razor templates</span></span>

<span data-ttu-id="26d13-486">Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor.</span><span class="sxs-lookup"><span data-stu-id="26d13-486">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="26d13-487">Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="26d13-487">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="26d13-488">Následující příklad ukazuje, jak zadat `RenderFragment` a `RenderFragment<T>` hodnoty a vykreslit šablony přímo v součásti.</span><span class="sxs-lookup"><span data-stu-id="26d13-488">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="26d13-489">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="26d13-489">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="26d13-490">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="26d13-490">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="26d13-491">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="26d13-491">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="26d13-492">`Microsoft.AspNetCore.Components.RenderTree`poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.</span><span class="sxs-lookup"><span data-stu-id="26d13-492">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="26d13-493">Použití aplikace `RenderTreeBuilder` k vytvoření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="26d13-493">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="26d13-494">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="26d13-494">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="26d13-495">Vezměte v úvahu `PetDetails` následující součást, kterou je možné ručně vytvořit do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="26d13-495">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    private string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="26d13-496">V následujícím příkladu smyčka v `CreateComponent` metodě generuje tři `PetDetails` komponenty.</span><span class="sxs-lookup"><span data-stu-id="26d13-496">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="26d13-497">Při volání `RenderTreeBuilder` metod pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="26d13-497">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="26d13-498">Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání.</span><span class="sxs-lookup"><span data-stu-id="26d13-498">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="26d13-499">Při vytváření komponenty s `RenderTreeBuilder` metodami nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="26d13-499">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="26d13-500">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="26d13-500">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="26d13-501">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="26d13-501">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="26d13-502">`BuiltContent`část</span><span class="sxs-lookup"><span data-stu-id="26d13-502">`BuiltContent` component:</span></span>

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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="26d13-503">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="26d13-503">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="26d13-504">Soubory `.razor` Blazor jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="26d13-504">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="26d13-505">Tato možnost je potenciálně skvělou výhodou `.razor` , protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="26d13-505">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="26d13-506">Hlavní příklad těchto vylepšení zahrnuje pořadová *čísla*.</span><span class="sxs-lookup"><span data-stu-id="26d13-506">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="26d13-507">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="26d13-507">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="26d13-508">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="26d13-508">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="26d13-509">Vezměte v úvahu následující `.razor` jednoduchý soubor:</span><span class="sxs-lookup"><span data-stu-id="26d13-509">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="26d13-510">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="26d13-510">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="26d13-511">V případě, že se kód spustí poprvé, v `someFlag` případě `true`, že je, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="26d13-511">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="26d13-512">Sequence</span><span class="sxs-lookup"><span data-stu-id="26d13-512">Sequence</span></span> | <span data-ttu-id="26d13-513">type</span><span class="sxs-lookup"><span data-stu-id="26d13-513">Type</span></span>      | <span data-ttu-id="26d13-514">Data</span><span class="sxs-lookup"><span data-stu-id="26d13-514">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="26d13-515">0</span><span class="sxs-lookup"><span data-stu-id="26d13-515">0</span></span>        | <span data-ttu-id="26d13-516">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="26d13-516">Text node</span></span> | <span data-ttu-id="26d13-517">První</span><span class="sxs-lookup"><span data-stu-id="26d13-517">First</span></span>  |
| <span data-ttu-id="26d13-518">1</span><span class="sxs-lookup"><span data-stu-id="26d13-518">1</span></span>        | <span data-ttu-id="26d13-519">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="26d13-519">Text node</span></span> | <span data-ttu-id="26d13-520">Sekunda</span><span class="sxs-lookup"><span data-stu-id="26d13-520">Second</span></span> |

<span data-ttu-id="26d13-521">Představte `false`si, že `someFlag` se zobrazí a značka se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="26d13-521">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="26d13-522">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="26d13-522">This time, the builder receives:</span></span>

| <span data-ttu-id="26d13-523">Sequence</span><span class="sxs-lookup"><span data-stu-id="26d13-523">Sequence</span></span> | <span data-ttu-id="26d13-524">type</span><span class="sxs-lookup"><span data-stu-id="26d13-524">Type</span></span>       | <span data-ttu-id="26d13-525">Data</span><span class="sxs-lookup"><span data-stu-id="26d13-525">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="26d13-526">1</span><span class="sxs-lookup"><span data-stu-id="26d13-526">1</span></span>        | <span data-ttu-id="26d13-527">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="26d13-527">Text node</span></span>  | <span data-ttu-id="26d13-528">Sekunda</span><span class="sxs-lookup"><span data-stu-id="26d13-528">Second</span></span> |

<span data-ttu-id="26d13-529">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:</span><span class="sxs-lookup"><span data-stu-id="26d13-529">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="26d13-530">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="26d13-530">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="26d13-531">Co je špatné, pokud generujete pořadová čísla programově</span><span class="sxs-lookup"><span data-stu-id="26d13-531">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="26d13-532">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="26d13-532">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="26d13-533">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="26d13-533">Now, the first output is:</span></span>

| <span data-ttu-id="26d13-534">Sequence</span><span class="sxs-lookup"><span data-stu-id="26d13-534">Sequence</span></span> | <span data-ttu-id="26d13-535">type</span><span class="sxs-lookup"><span data-stu-id="26d13-535">Type</span></span>      | <span data-ttu-id="26d13-536">Data</span><span class="sxs-lookup"><span data-stu-id="26d13-536">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="26d13-537">0</span><span class="sxs-lookup"><span data-stu-id="26d13-537">0</span></span>        | <span data-ttu-id="26d13-538">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="26d13-538">Text node</span></span> | <span data-ttu-id="26d13-539">První</span><span class="sxs-lookup"><span data-stu-id="26d13-539">First</span></span>  |
| <span data-ttu-id="26d13-540">1</span><span class="sxs-lookup"><span data-stu-id="26d13-540">1</span></span>        | <span data-ttu-id="26d13-541">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="26d13-541">Text node</span></span> | <span data-ttu-id="26d13-542">Sekunda</span><span class="sxs-lookup"><span data-stu-id="26d13-542">Second</span></span> |

<span data-ttu-id="26d13-543">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="26d13-543">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="26d13-544">`someFlag`je `false` ve druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="26d13-544">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="26d13-545">Sequence</span><span class="sxs-lookup"><span data-stu-id="26d13-545">Sequence</span></span> | <span data-ttu-id="26d13-546">type</span><span class="sxs-lookup"><span data-stu-id="26d13-546">Type</span></span>      | <span data-ttu-id="26d13-547">Data</span><span class="sxs-lookup"><span data-stu-id="26d13-547">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="26d13-548">0</span><span class="sxs-lookup"><span data-stu-id="26d13-548">0</span></span>        | <span data-ttu-id="26d13-549">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="26d13-549">Text node</span></span> | <span data-ttu-id="26d13-550">Sekunda</span><span class="sxs-lookup"><span data-stu-id="26d13-550">Second</span></span> |

<span data-ttu-id="26d13-551">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="26d13-551">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="26d13-552">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="26d13-552">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="26d13-553">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="26d13-553">Remove the second text node.</span></span>

<span data-ttu-id="26d13-554">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` byly větve a cykly přítomny v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="26d13-554">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="26d13-555">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="26d13-555">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="26d13-556">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="26d13-556">This is a trivial example.</span></span> <span data-ttu-id="26d13-557">Ve složitějších případech se složitými a hluboce vnořenými strukturami, a to hlavně pomocí smyček, náklady na výkon jsou závažnější.</span><span class="sxs-lookup"><span data-stu-id="26d13-557">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="26d13-558">Místo toho, aby se ihned identifikovaly, které bloky nebo větve smyček byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování a obvykle sestavovat mnohem delší úpravu skriptů, protože je nepřehledně neinformován o tom, jak staré a nové struktury vzájemná vazba.</span><span class="sxs-lookup"><span data-stu-id="26d13-558">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="26d13-559">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="26d13-559">Guidance and conclusions</span></span>

* <span data-ttu-id="26d13-560">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="26d13-560">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="26d13-561">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="26d13-561">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="26d13-562">Nepište dlouhé bloky ručně implementované `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="26d13-562">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="26d13-563">Preferovat `.razor` soubory a umožněte kompilátoru, aby se zabývat pořadovým číslem.</span><span class="sxs-lookup"><span data-stu-id="26d13-563">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="26d13-564">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="26d13-564">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="26d13-565">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="26d13-565">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="26d13-566">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="26d13-566">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="26d13-567">Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="26d13-567">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="26d13-568">Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro `.razor` vývojáře, kteří vytváří soubory.</span><span class="sxs-lookup"><span data-stu-id="26d13-568">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>
