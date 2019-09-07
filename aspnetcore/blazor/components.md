---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/components
ms.openlocfilehash: e877abfb568f71046c3603cac5e888e99ffc8d15
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800417"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="31d48-103">Vytváření a používání ASP.NET Corech komponent Razor</span><span class="sxs-lookup"><span data-stu-id="31d48-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="31d48-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="31d48-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="31d48-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="31d48-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="31d48-106">Aplikace Blazor jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="31d48-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="31d48-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="31d48-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="31d48-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="31d48-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="31d48-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="31d48-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="31d48-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="31d48-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="31d48-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="31d48-111">Component classes</span></span>

<span data-ttu-id="31d48-112">Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="31d48-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="31d48-113">Komponenta v Blazor je formálně označována jako *Komponenta Razor*.</span><span class="sxs-lookup"><span data-stu-id="31d48-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="31d48-114">Název součásti musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="31d48-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="31d48-115">Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.</span><span class="sxs-lookup"><span data-stu-id="31d48-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="31d48-116">Komponenty mohou být vytvořeny pomocí přípony souboru *. cshtml* , pokud jsou soubory označeny jako soubory komponenty Razor pomocí `_RazorComponentInclude` vlastnosti MSBuild.</span><span class="sxs-lookup"><span data-stu-id="31d48-116">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="31d48-117">Například aplikace, která určuje, že všechny soubory *. cshtml* ve složce *Pages* by měly být považovány za soubory součástí Razor:</span><span class="sxs-lookup"><span data-stu-id="31d48-117">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

<span data-ttu-id="31d48-118">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="31d48-118">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="31d48-119">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="31d48-119">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="31d48-120">Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-120">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="31d48-121">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="31d48-121">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="31d48-122">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="31d48-122">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="31d48-123">`@code` V bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="31d48-123">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="31d48-124">Je přípustný více `@code` než jeden blok.</span><span class="sxs-lookup"><span data-stu-id="31d48-124">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="31d48-125">V předchozích verzích Preview ASP.NET Core 3,0 `@functions` byly bloky použity pro stejný účel jako `@code` bloky v součástech Razor.</span><span class="sxs-lookup"><span data-stu-id="31d48-125">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="31d48-126">`@functions`bloky nadále fungují v součástech Razor, ale doporučujeme použít `@code` blok v ASP.NET Core 3,0 Preview 6 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="31d48-126">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="31d48-127">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají `@`na.</span><span class="sxs-lookup"><span data-stu-id="31d48-127">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="31d48-128">Například C# pole se vykreslí pomocí předpony `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="31d48-128">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="31d48-129">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="31d48-129">The following example evaluates and renders:</span></span>

* <span data-ttu-id="31d48-130">`_headingFontStyle`do hodnoty vlastnosti CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="31d48-130">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="31d48-131">`_headingText`k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="31d48-131">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="31d48-132">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="31d48-132">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="31d48-133">Blazor pak porovná nový strom vykreslování s předchozí a použije všechny změny v model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="31d48-133">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="31d48-134">Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="31d48-134">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="31d48-135">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="31d48-135">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="31d48-136">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="31d48-136">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="31d48-137">Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky do nadřazené komponenty nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="31d48-137">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="31d48-138">Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je `WebApplication`kořenový obor názvů aplikace:</span><span class="sxs-lookup"><span data-stu-id="31d48-138">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="31d48-139">Integrace součástí do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="31d48-139">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="31d48-140">Použijte komponenty se stávajícími Razor Pages a MVC aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="31d48-140">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="31d48-141">Aby bylo možné použít součásti Razor, není nutné přepsat existující stránky ani zobrazení.</span><span class="sxs-lookup"><span data-stu-id="31d48-141">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="31d48-142">Po vykreslení stránky nebo zobrazení jsou komponenty předem vygenerovány ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="31d48-142">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="31d48-143">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte `RenderComponentAsync<TComponent>` pomocnou metodu HTML:</span><span class="sxs-lookup"><span data-stu-id="31d48-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="31d48-144">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="31d48-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="31d48-145">Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="31d48-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="31d48-146">Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="31d48-147">Další informace o tom, jak se komponenty vykreslují a stav komponenty se spravuje v Blazorch aplikacích na straně serveru, <xref:blazor/hosting-models> najdete v článku.</span><span class="sxs-lookup"><span data-stu-id="31d48-147">For more information on how components are rendered and component state is managed in Blazor server-side apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="31d48-148">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="31d48-148">Use components</span></span>

<span data-ttu-id="31d48-149">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="31d48-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="31d48-150">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="31d48-151">Vazba atributu rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="31d48-151">Attribute binding is case sensitive.</span></span> <span data-ttu-id="31d48-152">Například `@bind` je platný a `@Bind` je neplatný.</span><span class="sxs-lookup"><span data-stu-id="31d48-152">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="31d48-153">Následující kód v *indexu. Razor* vykreslí `HeadingComponent` instanci:</span><span class="sxs-lookup"><span data-stu-id="31d48-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="31d48-154">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="31d48-154">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="31d48-155">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="31d48-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="31d48-156">`@using` Přidání příkazu pro obor názvů součásti zpřístupňuje komponentu, což odstraní upozornění.</span><span class="sxs-lookup"><span data-stu-id="31d48-156">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="31d48-157">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="31d48-157">Component parameters</span></span>

<span data-ttu-id="31d48-158">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s `[Parameter]` atributem.</span><span class="sxs-lookup"><span data-stu-id="31d48-158">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="31d48-159">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="31d48-159">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="31d48-160">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="31d48-160">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="31d48-161">V následujícím příkladu `ParentComponent` nastaví hodnotu `Title` vlastnosti `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="31d48-161">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="31d48-162">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="31d48-162">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="31d48-163">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="31d48-163">Child content</span></span>

<span data-ttu-id="31d48-164">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-164">Components can set the content of another component.</span></span> <span data-ttu-id="31d48-165">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="31d48-165">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="31d48-166">V následujícím příkladu `ChildComponent` `ChildContent` má vlastnost, která představuje `RenderFragment`, který představuje segment uživatelského rozhraní pro vykreslení.</span><span class="sxs-lookup"><span data-stu-id="31d48-166">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="31d48-167">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="31d48-167">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="31d48-168">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v `panel-body`panelu Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="31d48-168">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="31d48-169">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="31d48-169">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="31d48-170">Vlastnost, která přijímá `RenderFragment` obsah, musí být `ChildContent` pojmenována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="31d48-170">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="31d48-171">Následující `ParentComponent` obsah vám může poskytnout obsah pro `ChildComponent` vykreslování `<ChildComponent>` umístěním obsahu uvnitř značek.</span><span class="sxs-lookup"><span data-stu-id="31d48-171">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="31d48-172">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="31d48-172">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="31d48-173">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="31d48-173">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="31d48-174">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="31d48-174">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="31d48-175">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí [@attributes](xref:mvc/views/razor#attributes) direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="31d48-175">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="31d48-176">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="31d48-176">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="31d48-177">Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="31d48-177">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="31d48-178">V `<input>` následujícím příkladu první prvek (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="31d48-178">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="31d48-179">Typ parametru musí být implementován `IEnumerable<KeyValuePair<string, object>>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="31d48-179">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="31d48-180">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="31d48-180">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="31d48-181">Vykreslené `<input>` elementy pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="31d48-181">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="31d48-182">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí `[Parameter]` atributu `CaptureUnmatchedValues` s vlastností nastavenou na `true`:</span><span class="sxs-lookup"><span data-stu-id="31d48-182">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="31d48-183">`CaptureUnmatchedValues` Vlastnost on`[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="31d48-183">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="31d48-184">Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="31d48-184">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="31d48-185">Typ vlastnosti používaný pomocí `CaptureUnmatchedValues` musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="31d48-185">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="31d48-186">`IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="31d48-186">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="31d48-187">Datová vazba</span><span class="sxs-lookup"><span data-stu-id="31d48-187">Data binding</span></span>

<span data-ttu-id="31d48-188">Datové vazby na součásti a elementy modelu DOM jsou provedeny [@bind](xref:mvc/views/razor#bind) atributem.</span><span class="sxs-lookup"><span data-stu-id="31d48-188">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="31d48-189">V následujícím příkladu je svázáno `_italicsCheck` pole se zaškrtnutým stavem zaškrtávací políčko:</span><span class="sxs-lookup"><span data-stu-id="31d48-189">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="31d48-190">Když je políčko zaškrtnuté a políčko je zaškrtnuté, hodnota vlastnosti se aktualizuje na `true` a `false`v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="31d48-190">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="31d48-191">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="31d48-191">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="31d48-192">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se v uživatelském rozhraní obvykle projeví aktualizace vlastností.</span><span class="sxs-lookup"><span data-stu-id="31d48-192">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="31d48-193">Použití `@bind` s`CurrentValue` vlastností(`<input @bind="CurrentValue" />`) je v podstatě ekvivalentem následujícího:</span><span class="sxs-lookup"><span data-stu-id="31d48-193">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="31d48-194">Při vykreslení `value` komponenty `CurrentValue` z vlastnosti input element přichází.</span><span class="sxs-lookup"><span data-stu-id="31d48-194">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="31d48-195">Když uživatel zadá do textového pole, `onchange` událost se aktivuje `CurrentValue` a vlastnost je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="31d48-195">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="31d48-196">Ve skutečnosti je generování kódu trochu složitější, protože `@bind` zpracovává několik případů, kde jsou prováděny převody typu.</span><span class="sxs-lookup"><span data-stu-id="31d48-196">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="31d48-197">V zásadě `@bind` přidruží aktuální hodnotu výrazu `value` k atributu a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="31d48-197">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="31d48-198">`onchange` Kromě zpracování[@bind-value:event](xref:mvc/views/razor#bind)událostí pomocí `@bind` syntaxe lze vlastnost nebo pole svázat pomocí [@bind-value](xref:mvc/views/razor#bind) jiných událostí `event` zadáním atributu s parametrem ().</span><span class="sxs-lookup"><span data-stu-id="31d48-198">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="31d48-199">Následující příklad váže `CurrentValue` vlastnost `oninput` pro událost:</span><span class="sxs-lookup"><span data-stu-id="31d48-199">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="31d48-200">Na rozdíl `onchange`od, která je aktivována, když prvek ztratí `oninput` fokus, je aktivována při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="31d48-200">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="31d48-201">**Globalizace**</span><span class="sxs-lookup"><span data-stu-id="31d48-201">**Globalization**</span></span>

<span data-ttu-id="31d48-202">`@bind`hodnoty jsou formátovány pro zobrazení a analýzu pomocí pravidel aktuální jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="31d48-202">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="31d48-203">K aktuální jazykové verzi je možné přistupovat z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="31d48-203">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="31d48-204">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující typy polí (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="31d48-204">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="31d48-205">Předchozí typy polí:</span><span class="sxs-lookup"><span data-stu-id="31d48-205">The preceding field types:</span></span>

* <span data-ttu-id="31d48-206">Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="31d48-206">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="31d48-207">Text s volným formulářem nemůže obsahovat.</span><span class="sxs-lookup"><span data-stu-id="31d48-207">Can't contain free-form text.</span></span>
* <span data-ttu-id="31d48-208">Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="31d48-208">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="31d48-209">Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány v Blazor, protože nejsou podporovány ve všech hlavních prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="31d48-209">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="31d48-210">`@bind``@bind:culture` podporuje parametr <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro zajištění analýzy a formátování hodnoty.</span><span class="sxs-lookup"><span data-stu-id="31d48-210">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="31d48-211">Specifikace jazykové verze není doporučena při použití `date` typů `number` polí a.</span><span class="sxs-lookup"><span data-stu-id="31d48-211">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="31d48-212">`date`a `number` mají integrovanou podporu Blazor, která poskytuje požadovanou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="31d48-212">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="31d48-213">Informace o tom, jak nastavit jazykovou verzi uživatele, najdete v části [lokalizace](#localization) .</span><span class="sxs-lookup"><span data-stu-id="31d48-213">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="31d48-214">**Řetězce formátu**</span><span class="sxs-lookup"><span data-stu-id="31d48-214">**Format strings**</span></span>

<span data-ttu-id="31d48-215">Datové vazby fungují s <xref:System.DateTime> řetězci formátu pomocí [@bind:format](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="31d48-215">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="31d48-216">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="31d48-216">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="31d48-217">V předchozím kódu `<input>` je výchozí `text`typ pole (`type`) elementu.</span><span class="sxs-lookup"><span data-stu-id="31d48-217">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="31d48-218">`@bind:format`je podporováno pro vázání následujících typů rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="31d48-218">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="31d48-219"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="31d48-219"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="31d48-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="31d48-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="31d48-221">Atribut určuje formát data, který se má použít `value` pro `<input>` element. `@bind:format`</span><span class="sxs-lookup"><span data-stu-id="31d48-221">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="31d48-222">Formát je také použit k analýze hodnoty při `onchange` výskytu události.</span><span class="sxs-lookup"><span data-stu-id="31d48-222">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="31d48-223">Zadání formátu pro `date` typ pole se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat.</span><span class="sxs-lookup"><span data-stu-id="31d48-223">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="31d48-224">**Parametry součásti**</span><span class="sxs-lookup"><span data-stu-id="31d48-224">**Component parameters**</span></span>

<span data-ttu-id="31d48-225">Vazba rozpoznává parametry komponenty, `@bind-{property}` kde může svázat hodnotu vlastnosti napříč komponentami.</span><span class="sxs-lookup"><span data-stu-id="31d48-225">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="31d48-226">Následující podřízená komponenta (`ChildComponent`) `Year` má parametr komponenty a `YearChanged` zpětné volání:</span><span class="sxs-lookup"><span data-stu-id="31d48-226">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="31d48-227">`EventCallback<T>`je vysvětleno v části [vnořenou eventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="31d48-227">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="31d48-228">Následující nadřazená komponenta používá `ChildComponent` a váže `ParentYear` parametr `Year` z nadřazené položky k parametru v podřízené komponentě:</span><span class="sxs-lookup"><span data-stu-id="31d48-228">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="31d48-229">Načítání kódu `ParentComponent` generuje následující značky:</span><span class="sxs-lookup"><span data-stu-id="31d48-229">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="31d48-230">Pokud je hodnota `ParentYear` vlastnosti změněna výběrem tlačítka `ParentComponent`v `ChildComponent` , `Year` vlastnost je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="31d48-230">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="31d48-231">Nová hodnota `Year` se vykreslí v uživatelském rozhraní, `ParentComponent` když se znovu vykreslí:</span><span class="sxs-lookup"><span data-stu-id="31d48-231">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="31d48-232">Parametr je svázán, protože má doprovodnou `YearChanged` událost, která `Year` odpovídá typu parametru. `Year`</span><span class="sxs-lookup"><span data-stu-id="31d48-232">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="31d48-233">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` má v podstatě ekvivalent zápisu:</span><span class="sxs-lookup"><span data-stu-id="31d48-233">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="31d48-234">Obecně platí, že vlastnost může být svázána s odpovídající obslužnou rutinou `@bind-property:event` události pomocí atributu.</span><span class="sxs-lookup"><span data-stu-id="31d48-234">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="31d48-235">Vlastnost `MyProp` může být například svázána s `MyEventHandler` použitím následujících dvou atributů:</span><span class="sxs-lookup"><span data-stu-id="31d48-235">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="31d48-236">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="31d48-236">Event handling</span></span>

<span data-ttu-id="31d48-237">Komponenty Razor poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="31d48-237">Razor components provide event handling features.</span></span> <span data-ttu-id="31d48-238">Pro atribut elementu HTML s názvem `on{event}` ( `onclick` například a `onsubmit`) s hodnotou typu delegáta, komponenty Razor považují hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="31d48-238">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="31d48-239">Název atributu má vždycky formát [ @on{Event}](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="31d48-239">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="31d48-240">Následující kód volá `UpdateHeading` metodu, pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="31d48-240">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="31d48-241">Následující kód volá `CheckChanged` metodu, když je zaškrtávací políčko změněno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="31d48-241">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="31d48-242">Obslužné rutiny událostí mohou být také asynchronní a <xref:System.Threading.Tasks.Task>vracet.</span><span class="sxs-lookup"><span data-stu-id="31d48-242">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="31d48-243">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="31d48-243">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="31d48-244">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="31d48-244">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="31d48-245">V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="31d48-245">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

### <a name="event-argument-types"></a><span data-ttu-id="31d48-246">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="31d48-246">Event argument types</span></span>

<span data-ttu-id="31d48-247">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="31d48-247">For some events, event argument types are permitted.</span></span> <span data-ttu-id="31d48-248">Pokud přístup k některému z těchto typů událostí není nezbytný, není nutné ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="31d48-248">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="31d48-249">Podporovaná vlastnost [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) je uvedena v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="31d48-249">Supported [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) are shown in the following table.</span></span>

| <span data-ttu-id="31d48-250">Událost</span><span class="sxs-lookup"><span data-stu-id="31d48-250">Event</span></span> | <span data-ttu-id="31d48-251">Třída</span><span class="sxs-lookup"><span data-stu-id="31d48-251">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="31d48-252">Schránka</span><span class="sxs-lookup"><span data-stu-id="31d48-252">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="31d48-253">Přetažení</span><span class="sxs-lookup"><span data-stu-id="31d48-253">Drag</span></span>             | <span data-ttu-id="31d48-254">`DragEventArgs`&ndash; auchovávajípřetažená`DataTransferItem`datapoložky. `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="31d48-254">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="31d48-255">Chyba</span><span class="sxs-lookup"><span data-stu-id="31d48-255">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="31d48-256">Vybrána</span><span class="sxs-lookup"><span data-stu-id="31d48-256">Focus</span></span>            | <span data-ttu-id="31d48-257">`FocusEventArgs`Nezahrnuje podporu pro `relatedTarget`. &ndash;</span><span class="sxs-lookup"><span data-stu-id="31d48-257">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="31d48-258">`<input>`mění</span><span class="sxs-lookup"><span data-stu-id="31d48-258">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="31d48-259">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="31d48-259">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="31d48-260">Stisknut</span><span class="sxs-lookup"><span data-stu-id="31d48-260">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="31d48-261">Ukazatele myši</span><span class="sxs-lookup"><span data-stu-id="31d48-261">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="31d48-262">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="31d48-262">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="31d48-263">Průběh</span><span class="sxs-lookup"><span data-stu-id="31d48-263">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="31d48-264">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="31d48-264">Touch</span></span>            | <span data-ttu-id="31d48-265">`TouchEventArgs`&ndash; představujejedenkontaktníbodnazařízenícitlivém`TouchPoint` na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="31d48-265">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="31d48-266">Informace o vlastnostech a chování zpracování událostí v předchozí tabulce naleznete v tématu [třídy EventArgs v referenčním zdroji (ASPNET/AspNetCore Release/3.0-preview9)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="31d48-266">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0-preview9 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="31d48-267">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="31d48-267">Lambda expressions</span></span>

<span data-ttu-id="31d48-268">Lambda výrazy lze také použít:</span><span class="sxs-lookup"><span data-stu-id="31d48-268">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="31d48-269">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="31d48-269">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="31d48-270">Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události (`UIMouseEventArgs`) a jeho čísla tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="31d48-270">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="31d48-271">Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="31d48-271">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="31d48-272">V opačném případě se stejná proměnná používá ve všech výrazech lambda, což způsobuje `i`, že hodnota je stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="31d48-272">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="31d48-273">Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="31d48-273">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="31d48-274">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="31d48-274">EventCallback</span></span>

<span data-ttu-id="31d48-275">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde&mdash;k události podřízené komponenty, například když dojde k události v podřízeném objektu. `onclick`</span><span class="sxs-lookup"><span data-stu-id="31d48-275">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="31d48-276">Chcete-li zobrazit události napříč komponentami `EventCallback`, použijte.</span><span class="sxs-lookup"><span data-stu-id="31d48-276">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="31d48-277">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="31d48-277">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="31d48-278">V ukázkové aplikaci ukazuje, jak `EventCallback` je nastavena `onclick` obslužná rutina tlačítka pro příjem delegáta z ukázky `ParentComponent`. `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="31d48-278">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="31d48-279">Je zadaný s `UIMouseEventArgs` ,`onclick` který je vhodný pro událost z periferního zařízení: `EventCallback`</span><span class="sxs-lookup"><span data-stu-id="31d48-279">The `EventCallback` is typed with `UIMouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="31d48-280">Nastaví podřízený objekt `ShowMessage`najehometodu: `EventCallback<T>` `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="31d48-280">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="31d48-281">Když je vybráno tlačítko v `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="31d48-281">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="31d48-282">`ParentComponent`Je volána metoda.`ShowMessage`</span><span class="sxs-lookup"><span data-stu-id="31d48-282">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="31d48-283">`messageText`se aktualizuje a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="31d48-283">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="31d48-284">Volání `StateHasChanged` není vyžadováno v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="31d48-284">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="31d48-285">`StateHasChanged`je volána automaticky pro `ParentComponent`revykreslování, stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="31d48-285">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="31d48-286">`EventCallback`a `EventCallback<T>` povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="31d48-286">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="31d48-287">`EventCallback<T>`je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="31d48-287">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="31d48-288">`EventCallback`je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="31d48-288">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="31d48-289">`EventCallback` Vyvolat nebo `EventCallback<T>` s a`InvokeAsync` očekávat :<xref:System.Threading.Tasks.Task></span><span class="sxs-lookup"><span data-stu-id="31d48-289">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="31d48-290">Použití `EventCallback` a`EventCallback<T>` pro zpracování událostí a parametry komponenty vazby.</span><span class="sxs-lookup"><span data-stu-id="31d48-290">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="31d48-291">Preferovat silného typu `EventCallback<T>` přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="31d48-291">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="31d48-292">`EventCallback<T>`poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-292">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="31d48-293">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="31d48-293">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="31d48-294">Použijte `EventCallback` v případě, že zpětnému volání není předáno žádné číslo.</span><span class="sxs-lookup"><span data-stu-id="31d48-294">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="31d48-295">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="31d48-295">Capture references to components</span></span>

<span data-ttu-id="31d48-296">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="31d48-296">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="31d48-297">Zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="31d48-297">To capture a component reference:</span></span>

* <span data-ttu-id="31d48-298">[@ref](xref:mvc/views/razor#ref) Přidejte atribut do podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-298">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="31d48-299">Definujte pole stejného typu jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="31d48-299">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="31d48-300">Při vykreslení `loginDialog` komponenty je pole vyplněno `MyLoginDialog` instancí podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-300">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="31d48-301">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-301">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="31d48-302">Proměnná je naplněna pouze po vykreslení komponenty a její výstup `MyLoginDialog` obsahuje element. `loginDialog`</span><span class="sxs-lookup"><span data-stu-id="31d48-302">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="31d48-303">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="31d48-303">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="31d48-304">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte `OnAfterRenderAsync` metody `OnAfterRender` nebo.</span><span class="sxs-lookup"><span data-stu-id="31d48-304">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="31d48-305">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není to funkce [interoperability JavaScriptu](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="31d48-305">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="31d48-306">Odkazy na součásti nejsou předány kódu&mdash;jazyka JavaScript, které jsou používány pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="31d48-306">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="31d48-307">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="31d48-307">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="31d48-308">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="31d48-308">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="31d48-309">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="31d48-309">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="31d48-310">Vyvolat metody komponenty externě na stav aktualizace</span><span class="sxs-lookup"><span data-stu-id="31d48-310">Invoke component methods externally to update state</span></span>

<span data-ttu-id="31d48-311">Blazor používá `SynchronizationContext` k vykonání jediného logického vlákna.</span><span class="sxs-lookup"><span data-stu-id="31d48-311">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="31d48-312">V tomto `SynchronizationContext`případě jsou spouštěny metody životního cyklu komponenty a všechna zpětná volání událostí, která jsou aktivována nástrojem Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d48-312">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="31d48-313">V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte `InvokeAsync` metodu, která bude zaslána do `SynchronizationContext`Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d48-313">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="31d48-314">Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:</span><span class="sxs-lookup"><span data-stu-id="31d48-314">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

    public event Action<string, int, Task> Notify;
}
```

<span data-ttu-id="31d48-315">`NotifierService` Použití pro aktualizaci komponenty:</span><span class="sxs-lookup"><span data-stu-id="31d48-315">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
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

<span data-ttu-id="31d48-316">V předchozím příkladu `NotifierService` vyvolá `OnNotify` metodu komponenty mimo Blazor 's `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="31d48-316">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="31d48-317">`InvokeAsync`slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.</span><span class="sxs-lookup"><span data-stu-id="31d48-317">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="31d48-318">Použití \@klíče k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="31d48-318">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="31d48-319">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazor rozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="31d48-319">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="31d48-320">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="31d48-320">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="31d48-321">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="31d48-321">Consider the following example:</span></span>

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

<span data-ttu-id="31d48-322">Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="31d48-322">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="31d48-323">Když se komponenta znovu vykreslí, může `<DetailsEditor>` se tato součást změnit, aby `Details` přijímala jiné hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="31d48-323">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="31d48-324">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="31d48-324">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="31d48-325">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="31d48-325">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="31d48-326">Proces mapování lze řídit pomocí `@key` atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="31d48-326">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="31d48-327">`@key`způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="31d48-327">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="31d48-328">Když se `People` kolekce změní, rozdílový algoritmus zachovává přidružení mezi `<DetailsEditor>` instancemi a `person` instancemi:</span><span class="sxs-lookup"><span data-stu-id="31d48-328">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="31d48-329">Pokud se ze seznamu odstraní, z uživatelského rozhraní se odebere `<DetailsEditor>` jenom odpovídající instance. `People` `Person`</span><span class="sxs-lookup"><span data-stu-id="31d48-329">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="31d48-330">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="31d48-330">Other instances are left unchanged.</span></span>
* <span data-ttu-id="31d48-331">Pokud je vložena na nějaké místo v seznamu, je do příslušné `<DetailsEditor>` pozice vložena jedna nová instance. `Person`</span><span class="sxs-lookup"><span data-stu-id="31d48-331">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="31d48-332">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="31d48-332">Other instances are left unchanged.</span></span>
* <span data-ttu-id="31d48-333">Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="31d48-333">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="31d48-334">V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="31d48-334">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="31d48-335">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="31d48-335">Keys are local to each container element or component.</span></span> <span data-ttu-id="31d48-336">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="31d48-336">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="31d48-337">Kdy použít \@klíč</span><span class="sxs-lookup"><span data-stu-id="31d48-337">When to use \@key</span></span>

<span data-ttu-id="31d48-338">Obvykle má smysl použít `@key` při každém vykreslení seznamu (například `@foreach` v bloku) a existuje vhodná `@key`hodnota pro definování.</span><span class="sxs-lookup"><span data-stu-id="31d48-338">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="31d48-339">Můžete také použít `@key` , chcete-li zabránit Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="31d48-339">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

<span data-ttu-id="31d48-340">Pokud `@currentPerson` se změní `@key` , direktiva Attribute vynutí, aby Blazor `<div>` zahození celého a jeho následníků a znovu sestavil podstrom v uživatelském rozhraní s novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="31d48-340">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="31d48-341">To může být užitečné, pokud potřebujete zaručit, že se při `@currentPerson` změnách nezachovají stav uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="31d48-341">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="31d48-342">Kdy nepoužívat \@klíč</span><span class="sxs-lookup"><span data-stu-id="31d48-342">When not to use \@key</span></span>

<span data-ttu-id="31d48-343">Při rozdílech s se `@key`účtují náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="31d48-343">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="31d48-344">Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="31d48-344">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="31d48-345">`@key` I když se nepoužívá, Blazor zachová podřízený element a instance komponenty co nejvíc.</span><span class="sxs-lookup"><span data-stu-id="31d48-345">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="31d48-346">Jedinou výhodou použití `@key` je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="31d48-346">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="31d48-347">Jaké hodnoty se mají použít \@pro klíč</span><span class="sxs-lookup"><span data-stu-id="31d48-347">What values to use for \@key</span></span>

<span data-ttu-id="31d48-348">Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro `@key`:</span><span class="sxs-lookup"><span data-stu-id="31d48-348">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="31d48-349">Instance objektů modelu (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="31d48-349">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="31d48-350">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="31d48-350">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="31d48-351">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="31d48-351">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="31d48-352">Zajistěte, aby `@key` hodnoty použité pro nekolidovat.</span><span class="sxs-lookup"><span data-stu-id="31d48-352">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="31d48-353">Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty střetu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo komponenty na nové prvky nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-353">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="31d48-354">Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="31d48-354">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="31d48-355">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="31d48-355">Lifecycle methods</span></span>

<span data-ttu-id="31d48-356">`OnInitializedAsync`a `OnInitialized` spusťte kód pro inicializaci komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-356">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="31d48-357">Chcete-li provést asynchronní operaci, `OnInitializedAsync` použijte `await` a klíčové slovo pro operaci:</span><span class="sxs-lookup"><span data-stu-id="31d48-357">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="31d48-358">Pro synchronní operaci použijte `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="31d48-358">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="31d48-359">`OnParametersSetAsync`a `OnParametersSet` jsou volány, když komponenta přijímá parametry z nadřazené položky a hodnoty jsou přiřazeny vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="31d48-359">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="31d48-360">Tyto metody jsou spouštěny po inicializaci komponenty a pokaždé, když je vykreslena komponenta:</span><span class="sxs-lookup"><span data-stu-id="31d48-360">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="31d48-361">`OnAfterRenderAsync`a `OnAfterRender` jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-361">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="31d48-362">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="31d48-362">Element and component references are populated at this point.</span></span> <span data-ttu-id="31d48-363">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="31d48-363">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="31d48-364">`OnAfterRender`není *volána při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="31d48-364">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="31d48-365">Parametr pro `OnAfterRenderAsync` a`OnAfterRender` je: `firstRender`</span><span class="sxs-lookup"><span data-stu-id="31d48-365">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="31d48-366">`true` Nastaví se na první vyvolání instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-366">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="31d48-367">Zajistí, že se práce s inicializací provádí jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="31d48-367">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="31d48-368">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="31d48-368">Handle incomplete async actions at render</span></span>

<span data-ttu-id="31d48-369">Asynchronní akce provedené v událostech životního cyklu se možná nedokončily, než se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="31d48-369">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="31d48-370">Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty.</span><span class="sxs-lookup"><span data-stu-id="31d48-370">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="31d48-371">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="31d48-371">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="31d48-372">Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), `null`zatímco objekty jsou.</span><span class="sxs-lookup"><span data-stu-id="31d48-372">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="31d48-373">V komponentě `OnInitializedAsync` šablon Blazor je přepsáno na asynchronně příjem dat předpovědi (`forecasts`). `FetchData`</span><span class="sxs-lookup"><span data-stu-id="31d48-373">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="31d48-374">V takovém případě `forecasts` se uživateli zobrazí zpráva o načítání. `null`</span><span class="sxs-lookup"><span data-stu-id="31d48-374">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="31d48-375">`Task` Po úspěšnémdokončenísekomponentaznovuvykreslís`OnInitializedAsync` aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="31d48-375">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="31d48-376">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="31d48-376">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="31d48-377">Spustit kód před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="31d48-377">Execute code before parameters are set</span></span>

<span data-ttu-id="31d48-378">`SetParameters`lze přepsat pro spuštění kódu před nastavením parametrů:</span><span class="sxs-lookup"><span data-stu-id="31d48-378">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="31d48-379">Pokud `base.SetParameters` není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="31d48-379">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="31d48-380">Například příchozí parametry nemusejí být přiřazeny vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="31d48-380">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="31d48-381">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="31d48-381">Suppress refreshing of the UI</span></span>

<span data-ttu-id="31d48-382">`ShouldRender`lze přepsat pro potlačení aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="31d48-382">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="31d48-383">Pokud se implementace vrátí `true`, uživatelské rozhraní se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="31d48-383">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="31d48-384">I když `ShouldRender` je přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="31d48-384">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="31d48-385">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="31d48-385">Component disposal with IDisposable</span></span>

<span data-ttu-id="31d48-386">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="31d48-386">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="31d48-387">Následující komponenta používá `@implements IDisposable` `Dispose` a metodu:</span><span class="sxs-lookup"><span data-stu-id="31d48-387">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="31d48-388">Směrování</span><span class="sxs-lookup"><span data-stu-id="31d48-388">Routing</span></span>

<span data-ttu-id="31d48-389">Směrování v Blazor se dosahuje tak, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="31d48-389">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="31d48-390">Když je zkompilován soubor Razor s `@page` direktivou, vygenerovaná třída je <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> dána zadáním šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="31d48-390">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="31d48-391">V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="31d48-391">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="31d48-392">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="31d48-392">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="31d48-393">Následující komponenta reaguje na požadavky pro `/BlazorRoute` a: `/DifferentBlazorRoute`</span><span class="sxs-lookup"><span data-stu-id="31d48-393">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="31d48-394">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="31d48-394">Route parameters</span></span>

<span data-ttu-id="31d48-395">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v `@page` direktivě.</span><span class="sxs-lookup"><span data-stu-id="31d48-395">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="31d48-396">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="31d48-396">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="31d48-397">*Komponenta parametru směrování*:</span><span class="sxs-lookup"><span data-stu-id="31d48-397">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="31d48-398">Volitelné parametry nejsou podporované, takže se `@page` v předchozím příkladu použijí dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="31d48-398">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="31d48-399">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="31d48-399">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="31d48-400">Druhá `@page` direktiva `{text}` převezme parametr Route a `Text` přiřadí hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="31d48-400">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="31d48-401">Dědičnost základní třídy pro prostředí s kódem na pozadí</span><span class="sxs-lookup"><span data-stu-id="31d48-401">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="31d48-402">Soubory součástí kombinují značky HTML C# a zpracovávají kód ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="31d48-402">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="31d48-403">`@inherits` Direktiva se dá použít k poskytování aplikací Blazor s prostředím "kódu na pozadí", které odděluje označení komponenty od zpracování kódu.</span><span class="sxs-lookup"><span data-stu-id="31d48-403">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="31d48-404">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak může komponenta zdědit základní třídu, `BlazorRocksBase`k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-404">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="31d48-405">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="31d48-405">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="31d48-406">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="31d48-406">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="31d48-407">Základní třída by měla být odvozena od `ComponentBase`třídy.</span><span class="sxs-lookup"><span data-stu-id="31d48-407">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="31d48-408">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="31d48-408">Import components</span></span>

<span data-ttu-id="31d48-409">Obor názvů komponenty vytvořené pomocí Razor je založen na:</span><span class="sxs-lookup"><span data-stu-id="31d48-409">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="31d48-410">Projekt `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="31d48-410">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="31d48-411">Cesta z kořenového adresáře projektu k součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-411">The path from the project root to the component.</span></span> <span data-ttu-id="31d48-412">Například `ComponentsSample/Pages/Index.razor` je v oboru názvů `ComponentsSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="31d48-412">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="31d48-413">Komponenty následují C# pravidla vazeb názvů.</span><span class="sxs-lookup"><span data-stu-id="31d48-413">Components follow C# name binding rules.</span></span> <span data-ttu-id="31d48-414">V případě *indexu. Razor*jsou v oboru *ComponentsSample*všechny komponenty ve stejné složce, *stránkách*a nadřazené složce.</span><span class="sxs-lookup"><span data-stu-id="31d48-414">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="31d48-415">Komponenty definované v jiném oboru názvů lze převést do rozsahu pomocí direktivy [ \@using](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="31d48-415">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="31d48-416">Pokud ve složce `ComponentsSample/Shared/`existuje `NavMenu.razor`jiná součást, lze komponentu použít v `Index.razor` příkazu s následujícím `@using` příkazem:</span><span class="sxs-lookup"><span data-stu-id="31d48-416">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="31d48-417">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které odstraňují nutnost [ \@](xref:mvc/views/razor#using) direktivy using:</span><span class="sxs-lookup"><span data-stu-id="31d48-417">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="31d48-418">`global::` Kvalifikace není podporovaná.</span><span class="sxs-lookup"><span data-stu-id="31d48-418">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="31d48-419">Import komponent s `using` příkazy s aliasem ( `@using Foo = Bar`například) není podporován.</span><span class="sxs-lookup"><span data-stu-id="31d48-419">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="31d48-420">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="31d48-420">Partially qualified names aren't supported.</span></span> <span data-ttu-id="31d48-421">Například přidání `@using ComponentsSample` a `NavMenu.razor` odkazování pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="31d48-421">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="31d48-422">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="31d48-422">Conditional HTML element attributes</span></span>

<span data-ttu-id="31d48-423">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="31d48-423">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="31d48-424">Pokud je `false` hodnota nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="31d48-424">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="31d48-425">Pokud je `true`hodnota, je vygenerována hodnota atributu minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="31d48-425">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="31d48-426">V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="31d48-426">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="31d48-427">Pokud `IsCompleted` je`true`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="31d48-427">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="31d48-428">Pokud `IsCompleted` je`false`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="31d48-428">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="31d48-429">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="31d48-429">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="31d48-430">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="31d48-430">Raw HTML</span></span>

<span data-ttu-id="31d48-431">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="31d48-431">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="31d48-432">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="31d48-432">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="31d48-433">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="31d48-433">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="31d48-434">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="31d48-434">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="31d48-435">Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="31d48-435">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="31d48-436">Komponenty se šablonami</span><span class="sxs-lookup"><span data-stu-id="31d48-436">Templated components</span></span>

<span data-ttu-id="31d48-437">Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-437">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="31d48-438">Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-438">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="31d48-439">Mezi několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="31d48-439">A couple of examples include:</span></span>

* <span data-ttu-id="31d48-440">Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="31d48-440">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="31d48-441">Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="31d48-441">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="31d48-442">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="31d48-442">Template parameters</span></span>

<span data-ttu-id="31d48-443">Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo. `RenderFragment<T>`</span><span class="sxs-lookup"><span data-stu-id="31d48-443">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="31d48-444">Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="31d48-444">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="31d48-445">`RenderFragment<T>`převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.</span><span class="sxs-lookup"><span data-stu-id="31d48-445">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="31d48-446">`TableTemplate`část</span><span class="sxs-lookup"><span data-stu-id="31d48-446">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="31d48-447">Při použití šablonované komponenty lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="31d48-447">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="31d48-448">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="31d48-448">Template context parameters</span></span>

<span data-ttu-id="31d48-449">Argumenty součásti typu `RenderFragment<T>` předané jako elementy mají implicitní parametr s názvem `context` (například z předchozího příkladu `@context.PetId`kódu), ale můžete změnit název parametru pomocí `Context` atributu u podřízeného objektu. objekt.</span><span class="sxs-lookup"><span data-stu-id="31d48-449">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="31d48-450">V následujícím příkladu `RowTemplate` `Context` atribut prvku Určuje `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="31d48-450">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="31d48-451">Alternativně lze zadat `Context` atribut prvku komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-451">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="31d48-452">Zadaný `Context` atribut se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="31d48-452">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="31d48-453">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu).</span><span class="sxs-lookup"><span data-stu-id="31d48-453">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="31d48-454">V následujícím příkladu `Context` se atribut zobrazí `TableTemplate` na elementu a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="31d48-454">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="31d48-455">Komponenty s obecným typem</span><span class="sxs-lookup"><span data-stu-id="31d48-455">Generic-typed components</span></span>

<span data-ttu-id="31d48-456">Komponenty se šablonami jsou často typu obecně typované.</span><span class="sxs-lookup"><span data-stu-id="31d48-456">Templated components are often generically typed.</span></span> <span data-ttu-id="31d48-457">Například obecná `ListViewTemplate` komponenta může být použita k vykreslování `IEnumerable<T>` hodnot.</span><span class="sxs-lookup"><span data-stu-id="31d48-457">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="31d48-458">Chcete-li definovat obecné komponenty, použijte `@typeparam` direktivu k určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="31d48-458">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="31d48-459">Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="31d48-459">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="31d48-460">V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="31d48-460">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="31d48-461">V následujícím příkladu `TItem="Pet"` určuje typ:</span><span class="sxs-lookup"><span data-stu-id="31d48-461">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="31d48-462">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="31d48-462">Cascading values and parameters</span></span>

<span data-ttu-id="31d48-463">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="31d48-463">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="31d48-464">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="31d48-464">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="31d48-465">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="31d48-465">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="31d48-466">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="31d48-466">Theme example</span></span>

<span data-ttu-id="31d48-467">V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="31d48-467">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="31d48-468">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="31d48-468">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="31d48-469">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="31d48-469">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="31d48-470">`CascadingValue` Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="31d48-470">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="31d48-471">Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="31d48-471">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="31d48-472">`ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="31d48-472">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="31d48-473">Každá odvozená komponenta může tuto vlastnost spotřebovat `ThemeInfo` prostřednictvím kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="31d48-473">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="31d48-474">`CascadingValuesParametersLayout`část</span><span class="sxs-lookup"><span data-stu-id="31d48-474">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="31d48-475">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí `[CascadingParameter]` atributu.</span><span class="sxs-lookup"><span data-stu-id="31d48-475">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="31d48-476">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="31d48-476">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="31d48-477">V ukázkové aplikaci `CascadingValuesParametersTheme` váže `ThemeInfo` komponenta kaskádovou hodnotu k kaskádovým parametrům.</span><span class="sxs-lookup"><span data-stu-id="31d48-477">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="31d48-478">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="31d48-478">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="31d48-479">`CascadingValuesParametersTheme`část</span><span class="sxs-lookup"><span data-stu-id="31d48-479">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="31d48-480">Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` součást a odpovídající `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="31d48-480">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="31d48-481">V následujícím příkladu jsou dvě `CascadingValue` komponenty kaskádovitě různé `MyCascadingType` instance podle názvu:</span><span class="sxs-lookup"><span data-stu-id="31d48-481">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="31d48-482">V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="31d48-482">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="31d48-483">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="31d48-483">TabSet example</span></span>

<span data-ttu-id="31d48-484">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="31d48-484">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="31d48-485">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="31d48-485">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="31d48-486">Ukázková aplikace má `ITab` rozhraní, které implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="31d48-486">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="31d48-487">Komponenta používá komponentu, která obsahuje několik `Tab` komponent: `TabSet` `CascadingValuesParametersTabSet`</span><span class="sxs-lookup"><span data-stu-id="31d48-487">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="31d48-488">Podřízené `Tab` součásti nejsou explicitně předány jako parametry `TabSet`do.</span><span class="sxs-lookup"><span data-stu-id="31d48-488">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="31d48-489">Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu. `TabSet`</span><span class="sxs-lookup"><span data-stu-id="31d48-489">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="31d48-490">Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. `TabSet` Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu `TabSet` , komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta `Tab` podřízenými součástmi.</span><span class="sxs-lookup"><span data-stu-id="31d48-490">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="31d48-491">`TabSet`část</span><span class="sxs-lookup"><span data-stu-id="31d48-491">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="31d48-492">Podřízené komponenty `Tab` zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do `TabSet` souřadnice a, na které karty jsou aktivní.</span><span class="sxs-lookup"><span data-stu-id="31d48-492">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="31d48-493">`Tab`část</span><span class="sxs-lookup"><span data-stu-id="31d48-493">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="31d48-494">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="31d48-494">Razor templates</span></span>

<span data-ttu-id="31d48-495">Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor.</span><span class="sxs-lookup"><span data-stu-id="31d48-495">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="31d48-496">Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="31d48-496">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="31d48-497">Následující příklad ukazuje, jak zadat `RenderFragment` a `RenderFragment<T>` hodnoty a vykreslit šablony přímo v součásti.</span><span class="sxs-lookup"><span data-stu-id="31d48-497">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="31d48-498">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="31d48-498">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="31d48-499">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="31d48-499">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="31d48-500">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="31d48-500">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="31d48-501">`Microsoft.AspNetCore.Components.RenderTree`poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.</span><span class="sxs-lookup"><span data-stu-id="31d48-501">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="31d48-502">Použití aplikace `RenderTreeBuilder` k vytvoření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="31d48-502">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="31d48-503">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="31d48-503">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="31d48-504">Vezměte v úvahu `PetDetails` následující součást, kterou je možné ručně vytvořit do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="31d48-504">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="31d48-505">V následujícím příkladu smyčka v `CreateComponent` metodě generuje tři `PetDetails` komponenty.</span><span class="sxs-lookup"><span data-stu-id="31d48-505">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="31d48-506">Při volání `RenderTreeBuilder` metod pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="31d48-506">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="31d48-507">Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání.</span><span class="sxs-lookup"><span data-stu-id="31d48-507">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="31d48-508">Při vytváření komponenty s `RenderTreeBuilder` metodami nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="31d48-508">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="31d48-509">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="31d48-509">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="31d48-510">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="31d48-510">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="31d48-511">`BuiltContent`část</span><span class="sxs-lookup"><span data-stu-id="31d48-511">`BuiltContent` component:</span></span>

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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="31d48-512">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="31d48-512">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="31d48-513">Soubory `.razor` Blazor jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="31d48-513">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="31d48-514">Tato možnost je potenciálně skvělou výhodou `.razor` , protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="31d48-514">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="31d48-515">Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*.</span><span class="sxs-lookup"><span data-stu-id="31d48-515">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="31d48-516">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="31d48-516">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="31d48-517">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="31d48-517">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="31d48-518">Vezměte v úvahu následující `.razor` jednoduchý soubor:</span><span class="sxs-lookup"><span data-stu-id="31d48-518">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="31d48-519">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="31d48-519">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="31d48-520">V případě, že se kód spustí poprvé, v `someFlag` případě `true`, že je, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="31d48-520">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="31d48-521">Sequence</span><span class="sxs-lookup"><span data-stu-id="31d48-521">Sequence</span></span> | <span data-ttu-id="31d48-522">type</span><span class="sxs-lookup"><span data-stu-id="31d48-522">Type</span></span>      | <span data-ttu-id="31d48-523">Data</span><span class="sxs-lookup"><span data-stu-id="31d48-523">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="31d48-524">0</span><span class="sxs-lookup"><span data-stu-id="31d48-524">0</span></span>        | <span data-ttu-id="31d48-525">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="31d48-525">Text node</span></span> | <span data-ttu-id="31d48-526">První</span><span class="sxs-lookup"><span data-stu-id="31d48-526">First</span></span>  |
| <span data-ttu-id="31d48-527">1</span><span class="sxs-lookup"><span data-stu-id="31d48-527">1</span></span>        | <span data-ttu-id="31d48-528">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="31d48-528">Text node</span></span> | <span data-ttu-id="31d48-529">Sekunda</span><span class="sxs-lookup"><span data-stu-id="31d48-529">Second</span></span> |

<span data-ttu-id="31d48-530">Představte `false`si, že `someFlag` se zobrazí a značka se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="31d48-530">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="31d48-531">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="31d48-531">This time, the builder receives:</span></span>

| <span data-ttu-id="31d48-532">Sequence</span><span class="sxs-lookup"><span data-stu-id="31d48-532">Sequence</span></span> | <span data-ttu-id="31d48-533">type</span><span class="sxs-lookup"><span data-stu-id="31d48-533">Type</span></span>       | <span data-ttu-id="31d48-534">Data</span><span class="sxs-lookup"><span data-stu-id="31d48-534">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="31d48-535">1</span><span class="sxs-lookup"><span data-stu-id="31d48-535">1</span></span>        | <span data-ttu-id="31d48-536">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="31d48-536">Text node</span></span>  | <span data-ttu-id="31d48-537">Sekunda</span><span class="sxs-lookup"><span data-stu-id="31d48-537">Second</span></span> |

<span data-ttu-id="31d48-538">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:</span><span class="sxs-lookup"><span data-stu-id="31d48-538">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="31d48-539">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="31d48-539">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="31d48-540">Co je špatné, pokud generujete pořadová čísla programově</span><span class="sxs-lookup"><span data-stu-id="31d48-540">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="31d48-541">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="31d48-541">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="31d48-542">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="31d48-542">Now, the first output is:</span></span>

| <span data-ttu-id="31d48-543">Sequence</span><span class="sxs-lookup"><span data-stu-id="31d48-543">Sequence</span></span> | <span data-ttu-id="31d48-544">type</span><span class="sxs-lookup"><span data-stu-id="31d48-544">Type</span></span>      | <span data-ttu-id="31d48-545">Data</span><span class="sxs-lookup"><span data-stu-id="31d48-545">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="31d48-546">0</span><span class="sxs-lookup"><span data-stu-id="31d48-546">0</span></span>        | <span data-ttu-id="31d48-547">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="31d48-547">Text node</span></span> | <span data-ttu-id="31d48-548">První</span><span class="sxs-lookup"><span data-stu-id="31d48-548">First</span></span>  |
| <span data-ttu-id="31d48-549">1</span><span class="sxs-lookup"><span data-stu-id="31d48-549">1</span></span>        | <span data-ttu-id="31d48-550">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="31d48-550">Text node</span></span> | <span data-ttu-id="31d48-551">Sekunda</span><span class="sxs-lookup"><span data-stu-id="31d48-551">Second</span></span> |

<span data-ttu-id="31d48-552">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="31d48-552">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="31d48-553">`someFlag`je `false` ve druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="31d48-553">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="31d48-554">Sequence</span><span class="sxs-lookup"><span data-stu-id="31d48-554">Sequence</span></span> | <span data-ttu-id="31d48-555">type</span><span class="sxs-lookup"><span data-stu-id="31d48-555">Type</span></span>      | <span data-ttu-id="31d48-556">Data</span><span class="sxs-lookup"><span data-stu-id="31d48-556">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="31d48-557">0</span><span class="sxs-lookup"><span data-stu-id="31d48-557">0</span></span>        | <span data-ttu-id="31d48-558">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="31d48-558">Text node</span></span> | <span data-ttu-id="31d48-559">Sekunda</span><span class="sxs-lookup"><span data-stu-id="31d48-559">Second</span></span> |

<span data-ttu-id="31d48-560">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="31d48-560">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="31d48-561">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="31d48-561">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="31d48-562">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="31d48-562">Remove the second text node.</span></span>

<span data-ttu-id="31d48-563">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` byly větve a cykly přítomny v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="31d48-563">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="31d48-564">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="31d48-564">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="31d48-565">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="31d48-565">This is a trivial example.</span></span> <span data-ttu-id="31d48-566">Ve složitějších případech se složitými a hluboce vnořenými strukturami, a to hlavně pomocí smyček, náklady na výkon jsou závažnější.</span><span class="sxs-lookup"><span data-stu-id="31d48-566">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="31d48-567">Místo toho, aby se ihned identifikovaly, které bloky nebo větve smyček byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování a obvykle sestavovat mnohem delší úpravu skriptů, protože je nepřehledně neinformován o tom, jak staré a nové struktury vzájemná vazba.</span><span class="sxs-lookup"><span data-stu-id="31d48-567">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="31d48-568">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="31d48-568">Guidance and conclusions</span></span>

* <span data-ttu-id="31d48-569">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="31d48-569">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="31d48-570">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="31d48-570">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="31d48-571">Nepište dlouhé bloky ručně implementované `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="31d48-571">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="31d48-572">Preferovat `.razor` soubory a umožněte kompilátoru, aby se zabývat pořadovým číslem.</span><span class="sxs-lookup"><span data-stu-id="31d48-572">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="31d48-573">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="31d48-573">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="31d48-574">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="31d48-574">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="31d48-575">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="31d48-575">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="31d48-576">Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="31d48-576">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="31d48-577">Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro `.razor` vývojáře, kteří vytváří soubory.</span><span class="sxs-lookup"><span data-stu-id="31d48-577">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="31d48-578">Lokalizace</span><span class="sxs-lookup"><span data-stu-id="31d48-578">Localization</span></span>

<span data-ttu-id="31d48-579">Blazor aplikace na straně serveru jsou lokalizovány pomocí [middlewaru pro lokalizaci](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="31d48-579">Blazor server-side apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="31d48-580">Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.</span><span class="sxs-lookup"><span data-stu-id="31d48-580">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="31d48-581">Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="31d48-581">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="31d48-582">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="31d48-582">Cookies</span></span>](#cookies)
* [<span data-ttu-id="31d48-583">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="31d48-583">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="31d48-584">Další informace a příklady naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="31d48-584">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="31d48-585">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="31d48-585">Cookies</span></span>

<span data-ttu-id="31d48-586">Soubor cookie lokalizační kultury může zachovat jazykovou verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="31d48-586">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="31d48-587">Soubor cookie je vytvořen `OnGet` metodou stránky hostitele aplikace (*stránky/Host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="31d48-587">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="31d48-588">Middleware lokalizace přečte soubor cookie při následných požadavcích na nastavení jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="31d48-588">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="31d48-589">Použití souboru cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="31d48-589">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="31d48-590">Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="31d48-590">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="31d48-591">Proto je doporučený přístup použití souboru cookie lokalizační kultury.</span><span class="sxs-lookup"><span data-stu-id="31d48-591">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="31d48-592">Pokud je jazyková verze uložena v souboru cookie lokalizace, je možné použít jakoukoli techniku k přiřazení jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="31d48-592">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="31d48-593">Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte soubor cookie lokalizační kultury v rámci schématu aplikace.</span><span class="sxs-lookup"><span data-stu-id="31d48-593">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="31d48-594">Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze přečíst pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="31d48-594">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="31d48-595">Vytvořte soubor *Pages/Host. cshtml. cs* s následujícím obsahem v Blazor aplikaci na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="31d48-595">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor server-side app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="31d48-596">Lokalizace se zpracovává v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="31d48-596">Localization is handled in the app:</span></span>

1. <span data-ttu-id="31d48-597">Prohlížeč pošle do aplikace počáteční požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="31d48-597">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="31d48-598">Jazyková verze je přiřazena pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="31d48-598">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="31d48-599">Metoda v *_Host. cshtml. cs* uchovává v rámci odpovědi jazykovou verzi v souboru cookie. `OnGet`</span><span class="sxs-lookup"><span data-stu-id="31d48-599">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="31d48-600">Prohlížeč otevře připojení WebSocket a vytvoří interaktivní Blazor relaci na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="31d48-600">The browser opens a WebSocket connection to create an interactive Blazor server-side session.</span></span>
1. <span data-ttu-id="31d48-601">Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="31d48-601">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="31d48-602">Blazor relace na straně serveru začíná správnou jazykovou verzí.</span><span class="sxs-lookup"><span data-stu-id="31d48-602">The Blazor server-side session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="31d48-603">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="31d48-603">Provide UI to choose the culture</span></span>

<span data-ttu-id="31d48-604">K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* .</span><span class="sxs-lookup"><span data-stu-id="31d48-604">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="31d48-605">Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k&mdash;zabezpečenému prostředku, uživatel se přesměruje na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek.</span><span class="sxs-lookup"><span data-stu-id="31d48-605">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="31d48-606">Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler.</span><span class="sxs-lookup"><span data-stu-id="31d48-606">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="31d48-607">Řadič Nastaví vybranou jazykovou verzi uživatele na soubor cookie a přesměruje uživatele zpět na původní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="31d48-607">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="31d48-608">Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v souboru cookie a proveďte přesměrování zpátky na původní identifikátor URI:</span><span class="sxs-lookup"><span data-stu-id="31d48-608">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="31d48-609">Pomocí výsledku `LocalRedirect` akce zabráníte otevírání útoků přes přesměrování.</span><span class="sxs-lookup"><span data-stu-id="31d48-609">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="31d48-610">Další informace naleznete v tématu <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="31d48-610">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="31d48-611">Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:</span><span class="sxs-lookup"><span data-stu-id="31d48-611">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(UIChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="31d48-612">Použití scénářů lokalizace .NET v aplikacích Blazor</span><span class="sxs-lookup"><span data-stu-id="31d48-612">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="31d48-613">V aplikacích Blazor jsou k dispozici následující scénáře lokalizace a globalizace rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="31d48-613">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="31d48-614">. Systém prostředků netto</span><span class="sxs-lookup"><span data-stu-id="31d48-614">.NET's resources system</span></span>
* <span data-ttu-id="31d48-615">Formátování čísla a data specifické pro jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="31d48-615">Culture-specific number and date formatting</span></span>

<span data-ttu-id="31d48-616">`@bind` Funkce Blazor provádí globalizaci na základě aktuální jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="31d48-616">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="31d48-617">Další informace najdete v části [vázání dat](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="31d48-617">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="31d48-618">V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="31d48-618">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="31d48-619">`IStringLocalizer<>`*je podporováno* v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d48-619">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="31d48-620">`IHtmlLocalizer<>`lokalizace datových poznámek, `IViewLocalizer<>`a jsou ASP.NET Core scénáře MVC a **nejsou podporované** v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="31d48-620">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="31d48-621">Další informace naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="31d48-621">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="31d48-622">Obrázky ve formátu SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="31d48-622">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="31d48-623">Vzhledem k `<img>` tomu, že Blazor vykresluje HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) ( *. SVG*), jsou podporovány prostřednictvím značky:</span><span class="sxs-lookup"><span data-stu-id="31d48-623">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="31d48-624">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů ( *. CSS*):</span><span class="sxs-lookup"><span data-stu-id="31d48-624">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="31d48-625">Vložené značky SVG se však ve všech scénářích nepodporují.</span><span class="sxs-lookup"><span data-stu-id="31d48-625">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="31d48-626">Pokud `<svg>` značku přímo umístíte do souboru komponenty ( *. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů ještě není podporováno.</span><span class="sxs-lookup"><span data-stu-id="31d48-626">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="31d48-627">Například `<use>` značky nejsou aktuálně dodržovány a `@bind` nelze je použít s některými značkami SVG.</span><span class="sxs-lookup"><span data-stu-id="31d48-627">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="31d48-628">Očekáváme, že tato omezení vyřešíme v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="31d48-628">We expect to address these limitations in a future release.</span></span>
