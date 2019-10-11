---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/components
ms.openlocfilehash: 438b3802087e2ac3df4cbe69a700b878c1cbbf63
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037420"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="16656-103">Vytváření a používání ASP.NET Corech komponent Razor</span><span class="sxs-lookup"><span data-stu-id="16656-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="16656-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="16656-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="16656-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="16656-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="16656-106">Aplikace Blazor jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="16656-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="16656-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="16656-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="16656-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="16656-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="16656-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="16656-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="16656-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="16656-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="16656-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="16656-111">Component classes</span></span>

<span data-ttu-id="16656-112">Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="16656-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="16656-113">Komponenta v Blazor je formálně označována jako *Komponenta Razor*.</span><span class="sxs-lookup"><span data-stu-id="16656-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="16656-114">Název součásti musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="16656-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="16656-115">Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.</span><span class="sxs-lookup"><span data-stu-id="16656-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="16656-116">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="16656-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="16656-117">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="16656-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="16656-118">Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="16656-119">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="16656-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="16656-120">Členy třídy komponenty jsou definovány v bloku `@code`.</span><span class="sxs-lookup"><span data-stu-id="16656-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="16656-121">V bloku `@code` je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="16656-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="16656-122">Je přípustný více než jeden blok `@code`.</span><span class="sxs-lookup"><span data-stu-id="16656-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="16656-123">V předchozích verzích Preview ASP.NET Core 3,0 byly `@functions` bloků použity pro stejný účel jako bloky `@code` v součástech Razor.</span><span class="sxs-lookup"><span data-stu-id="16656-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="16656-124">bloky `@functions` nadále fungují v součástech Razor, ale doporučujeme použít blok `@code` v ASP.NET Core 3,0 Preview 6 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="16656-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="16656-125">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají na `@`.</span><span class="sxs-lookup"><span data-stu-id="16656-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="16656-126">Například C# pole se vykreslí pomocí prefixování `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="16656-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="16656-127">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="16656-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="16656-128">`_headingFontStyle` pro hodnotu vlastnosti CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="16656-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="16656-129">`_headingText` k obsahu prvku `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="16656-129">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="16656-130">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="16656-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="16656-131">Blazor pak porovná nový strom vykreslování s předchozí a použije všechny změny v model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="16656-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="16656-132">Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="16656-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="16656-133">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="16656-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="16656-134">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="16656-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="16656-135">Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky do nadřazené komponenty nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="16656-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="16656-136">Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je kořenový obor názvů aplikace `WebApplication`:</span><span class="sxs-lookup"><span data-stu-id="16656-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="16656-137">Integrace součástí do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="16656-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="16656-138">Použijte komponenty se stávajícími Razor Pages a MVC aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="16656-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="16656-139">Aby bylo možné použít součásti Razor, není nutné přepsat existující stránky ani zobrazení.</span><span class="sxs-lookup"><span data-stu-id="16656-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="16656-140">Po vykreslení stránky nebo zobrazení jsou komponenty předem vygenerovány ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="16656-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="16656-141">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte pomocnou metodu HTML `RenderComponentAsync<TComponent>`:</span><span class="sxs-lookup"><span data-stu-id="16656-141">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="16656-142">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="16656-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="16656-143">Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="16656-143">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="16656-144">Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="16656-144">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="16656-145">Další informace o tom, jak se komponenty vykreslují a stav komponenty se spravuje v aplikacích Blazor Server, najdete v článku o @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="16656-145">For more information on how components are rendered and component state is managed in Blazor Server apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="16656-146">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="16656-146">Use components</span></span>

<span data-ttu-id="16656-147">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="16656-147">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="16656-148">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="16656-148">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="16656-149">Vazba atributu rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="16656-149">Attribute binding is case sensitive.</span></span> <span data-ttu-id="16656-150">Například `@bind` je platný a `@Bind` je neplatné.</span><span class="sxs-lookup"><span data-stu-id="16656-150">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="16656-151">Následující kód v *indexu. Razor* vykreslí instanci `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="16656-151">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="16656-152">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="16656-152">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="16656-153">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="16656-153">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="16656-154">Přidání příkazu `@using` pro obor názvů součásti zpřístupňuje komponentu, což odstraní upozornění.</span><span class="sxs-lookup"><span data-stu-id="16656-154">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="16656-155">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="16656-155">Component parameters</span></span>

<span data-ttu-id="16656-156">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="16656-156">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="16656-157">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="16656-157">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="16656-158">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="16656-158">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="16656-159">V následujícím příkladu `ParentComponent` nastaví hodnotu vlastnosti `Title` `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="16656-159">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="16656-160">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="16656-160">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="16656-161">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="16656-161">Child content</span></span>

<span data-ttu-id="16656-162">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-162">Components can set the content of another component.</span></span> <span data-ttu-id="16656-163">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="16656-163">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="16656-164">V následujícím příkladu má `ChildComponent` vlastnost `ChildContent`, která představuje `RenderFragment`, která představuje segment uživatelského rozhraní, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="16656-164">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="16656-165">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="16656-165">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="16656-166">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v @no__t panelu zaváděcího programu-1.</span><span class="sxs-lookup"><span data-stu-id="16656-166">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="16656-167">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="16656-167">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="16656-168">Vlastnost, která přijímá obsah `RenderFragment`, musí mít název `ChildContent` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="16656-168">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="16656-169">Následující `ParentComponent` může poskytnout obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř značek `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="16656-169">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="16656-170">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="16656-170">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="16656-171">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="16656-171">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="16656-172">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="16656-172">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="16656-173">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí direktivy [@attributes](xref:mvc/views/razor#attributes) Razor.</span><span class="sxs-lookup"><span data-stu-id="16656-173">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="16656-174">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="16656-174">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="16656-175">Například může být zdlouhavé definovat atributy samostatně pro `<input>`, který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="16656-175">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="16656-176">V následujícím příkladu první prvek `<input>` (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý prvek `<input>` (`id="useAttributesDict"`) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="16656-176">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="16656-177">Typ parametru musí implementovat `IEnumerable<KeyValuePair<string, object>>` s klíči řetězce.</span><span class="sxs-lookup"><span data-stu-id="16656-177">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="16656-178">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="16656-178">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="16656-179">Vykreslené prvky `<input>` pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="16656-179">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="16656-180">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí atributu `[Parameter]` s vlastností `CaptureUnmatchedValues` nastavenou na `true`:</span><span class="sxs-lookup"><span data-stu-id="16656-180">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="16656-181">Vlastnost `CaptureUnmatchedValues` u `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="16656-181">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="16656-182">Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="16656-182">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="16656-183">Typ vlastnosti používaný s `CaptureUnmatchedValues` musí být možné přiřadit z `Dictionary<string, object>` s klíči řetězce.</span><span class="sxs-lookup"><span data-stu-id="16656-183">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="16656-184">v tomto scénáři jsou také možnosti `IEnumerable<KeyValuePair<string, object>>` nebo `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="16656-184">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="16656-185">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="16656-185">Data binding</span></span>

<span data-ttu-id="16656-186">Datové vazby na součásti a prvky modelu DOM jsou provedeny atributem [@bind](xref:mvc/views/razor#bind) .</span><span class="sxs-lookup"><span data-stu-id="16656-186">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="16656-187">Následující příklad váže pole `_italicsCheck` na zaškrtnutý stav zaškrtávacího políčka:</span><span class="sxs-lookup"><span data-stu-id="16656-187">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="16656-188">Když je políčko zaškrtnuté a políčko je zaškrtnuté, hodnota vlastnosti se aktualizuje na `true` a `false` v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="16656-188">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="16656-189">Zaškrtávací políčko se aktualizuje v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="16656-189">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="16656-190">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se v uživatelském rozhraní obvykle projeví aktualizace vlastností.</span><span class="sxs-lookup"><span data-stu-id="16656-190">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="16656-191">Použití `@bind` s vlastností `CurrentValue` (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentní následujícímu:</span><span class="sxs-lookup"><span data-stu-id="16656-191">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="16656-192">Když je komponenta vykreslena, `value` vstupního prvku pochází z vlastnosti `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="16656-192">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="16656-193">Pokud uživatel zadá do textového pole událost `onchange` a vlastnost `CurrentValue` je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="16656-193">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="16656-194">Ve skutečnosti je generování kódu trochu složitější, protože `@bind` zpracovává několik případů, kde jsou prováděny převody typů.</span><span class="sxs-lookup"><span data-stu-id="16656-194">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="16656-195">V zásadě `@bind` přidruží aktuální hodnotu výrazu k atributu `value` a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="16656-195">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="16656-196">Kromě zpracování událostí `onchange` se syntaxí `@bind` může být vlastnost nebo pole svázána s jinými událostmi, a to zadáním atributu [@bind-value](xref:mvc/views/razor#bind) s parametrem `event` ([@bind-value:event](xref:mvc/views/razor#bind)).</span><span class="sxs-lookup"><span data-stu-id="16656-196">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="16656-197">Následující příklad váže vlastnost `CurrentValue` pro událost `oninput`:</span><span class="sxs-lookup"><span data-stu-id="16656-197">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="16656-198">Na rozdíl od `onchange`, která je aktivována, když prvek ztratí fokus, `oninput` je aktivována při změně hodnoty textového pole.</span><span class="sxs-lookup"><span data-stu-id="16656-198">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="16656-199">**Hodnoty, které nelze analyzovat**</span><span class="sxs-lookup"><span data-stu-id="16656-199">**Unparsable values**</span></span>

<span data-ttu-id="16656-200">Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.</span><span class="sxs-lookup"><span data-stu-id="16656-200">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="16656-201">Vezměte v úvahu následující scénář:</span><span class="sxs-lookup"><span data-stu-id="16656-201">Consider the following scenario:</span></span>

* <span data-ttu-id="16656-202">Element `<input>` je vázán na typ `int` s počáteční hodnotou `123`:</span><span class="sxs-lookup"><span data-stu-id="16656-202">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="16656-203">Uživatel aktualizuje hodnotu prvku tak, aby na stránce `123.45` a změnil fokus prvku.</span><span class="sxs-lookup"><span data-stu-id="16656-203">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="16656-204">V předchozím scénáři je hodnota elementu vrácena na `123`.</span><span class="sxs-lookup"><span data-stu-id="16656-204">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="16656-205">Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123`, uživateli se rozumí, že jejich hodnota nebyla přijata.</span><span class="sxs-lookup"><span data-stu-id="16656-205">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="16656-206">Ve výchozím nastavení se vazba vztahuje na událost `onchange` elementu (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="16656-206">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="16656-207">K nastavení jiné události použijte `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`.</span><span class="sxs-lookup"><span data-stu-id="16656-207">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="16656-208">U události `oninput` (`@bind-value:event="oninput"`) dojde k opětovnému nastavování po stisku klávesových zkratek, které zavádí neanalyzovatelné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="16656-208">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="16656-209">Při cílení na událost `oninput` s typem vázaným @no__t -1 může uživatel zabránit psaní znaku `.`.</span><span class="sxs-lookup"><span data-stu-id="16656-209">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="16656-210">Znak `.` je okamžitě odebrán, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="16656-210">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="16656-211">K dispozici jsou situace, kdy vrácení hodnoty na událost `oninput` není ideální, například pokud by měl uživatel povoleno vymazat neanalyzovatelné hodnoty `<input>`.</span><span class="sxs-lookup"><span data-stu-id="16656-211">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="16656-212">K alternativám patří:</span><span class="sxs-lookup"><span data-stu-id="16656-212">Alternatives include:</span></span>

* <span data-ttu-id="16656-213">Nepoužívejte událost `oninput`.</span><span class="sxs-lookup"><span data-stu-id="16656-213">Don't use the `oninput` event.</span></span> <span data-ttu-id="16656-214">Použijte výchozí událost `onchange` (`@bind="{PROPERTY OR FIELD}"`), kde neplatnou hodnotu vrátí, dokud prvek neztratí fokus.</span><span class="sxs-lookup"><span data-stu-id="16656-214">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="16656-215">Vytvořte propojení s typem s možnou hodnotou null, například `int?` nebo `string` a poskytněte vlastní logiku pro zpracování neplatných položek.</span><span class="sxs-lookup"><span data-stu-id="16656-215">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="16656-216">Použijte [součást ověřování formuláře](xref:blazor/forms-validation), například `InputNumber` nebo `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="16656-216">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="16656-217">Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů.</span><span class="sxs-lookup"><span data-stu-id="16656-217">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="16656-218">Součásti pro ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="16656-218">Form validation components:</span></span>
  * <span data-ttu-id="16656-219">Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidružených `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="16656-219">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="16656-220">Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.</span><span class="sxs-lookup"><span data-stu-id="16656-220">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="16656-221">**Globalizace**</span><span class="sxs-lookup"><span data-stu-id="16656-221">**Globalization**</span></span>

<span data-ttu-id="16656-222">hodnoty `@bind` jsou formátovány pro zobrazení a analýzu pomocí pravidel aktuální jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="16656-222">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="16656-223">K aktuální jazykové verzi lze přistupovat z vlastnosti <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="16656-223">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="16656-224">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující typy polí (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="16656-224">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="16656-225">Předchozí typy polí:</span><span class="sxs-lookup"><span data-stu-id="16656-225">The preceding field types:</span></span>

* <span data-ttu-id="16656-226">Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="16656-226">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="16656-227">Text s volným formulářem nemůže obsahovat.</span><span class="sxs-lookup"><span data-stu-id="16656-227">Can't contain free-form text.</span></span>
* <span data-ttu-id="16656-228">Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="16656-228">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="16656-229">Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány v Blazor, protože nejsou podporovány ve všech hlavních prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="16656-229">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="16656-230">`@bind` podporuje parametr `@bind:culture` k poskytnutí <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro analýzu a formátování hodnoty.</span><span class="sxs-lookup"><span data-stu-id="16656-230">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="16656-231">Zadání jazykové verze se nedoporučuje při použití typů polí `date` a `number`.</span><span class="sxs-lookup"><span data-stu-id="16656-231">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="16656-232">`date` a `number` mají integrovanou podporu Blazor, která poskytuje požadovanou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="16656-232">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="16656-233">Informace o tom, jak nastavit jazykovou verzi uživatele, najdete v části [lokalizace](#localization) .</span><span class="sxs-lookup"><span data-stu-id="16656-233">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="16656-234">**Řetězce formátu**</span><span class="sxs-lookup"><span data-stu-id="16656-234">**Format strings**</span></span>

<span data-ttu-id="16656-235">Datové vazby fungují s řetězci formátu <xref:System.DateTime> pomocí [@bind:format](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="16656-235">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="16656-236">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="16656-236">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="16656-237">V předchozím kódu je typ pole elementu `<input>` (`type`) standardně `text`.</span><span class="sxs-lookup"><span data-stu-id="16656-237">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="16656-238">`@bind:format` se podporuje pro vazby následujících typů .NET:</span><span class="sxs-lookup"><span data-stu-id="16656-238">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="16656-239"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="16656-239"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="16656-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="16656-240"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="16656-241">Atribut `@bind:format` určuje formát data, který má být použit pro `value` prvku `<input>`.</span><span class="sxs-lookup"><span data-stu-id="16656-241">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="16656-242">Formát se používá také k analýze hodnoty při výskytu události `onchange`.</span><span class="sxs-lookup"><span data-stu-id="16656-242">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="16656-243">Zadání formátu pro typ pole `date` se nedoporučuje, protože Blazor má vestavěnou podporu pro formátování kalendářních dat.</span><span class="sxs-lookup"><span data-stu-id="16656-243">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="16656-244">**Parametry součásti**</span><span class="sxs-lookup"><span data-stu-id="16656-244">**Component parameters**</span></span>

<span data-ttu-id="16656-245">Vazba rozpoznává parametry komponenty, kde `@bind-{property}` může svázat hodnotu vlastnosti napříč komponentami.</span><span class="sxs-lookup"><span data-stu-id="16656-245">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="16656-246">Následující podřízená komponenta (`ChildComponent`) má parametr komponenty `Year` a zpětné volání `YearChanged`:</span><span class="sxs-lookup"><span data-stu-id="16656-246">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="16656-247">`EventCallback<T>` je vysvětleno v části [vnořenou eventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="16656-247">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="16656-248">Následující nadřazená komponenta používá `ChildComponent` a váže parametr `ParentYear` z nadřazené položky k parametru `Year` v podřízené komponentě:</span><span class="sxs-lookup"><span data-stu-id="16656-248">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="16656-249">Načtení `ParentComponent` vytvoří následující kód:</span><span class="sxs-lookup"><span data-stu-id="16656-249">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="16656-250">Pokud se hodnota vlastnosti `ParentYear` změní výběrem tlačítka v `ParentComponent`, bude aktualizována vlastnost `Year` `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="16656-250">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="16656-251">Nová hodnota `Year` se vykreslí v uživatelském rozhraní, když se `ParentComponent` znovu vykreslí:</span><span class="sxs-lookup"><span data-stu-id="16656-251">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="16656-252">Parametr `Year` je svázán, protože obsahuje doprovodnou událost `YearChanged`, která odpovídá typu parametru `Year`.</span><span class="sxs-lookup"><span data-stu-id="16656-252">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="16656-253">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` v podstatě ekvivalentem zápisu:</span><span class="sxs-lookup"><span data-stu-id="16656-253">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="16656-254">Obecně platí, že vlastnost může být svázána s odpovídající obslužnou rutinou události pomocí atributu `@bind-property:event`.</span><span class="sxs-lookup"><span data-stu-id="16656-254">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="16656-255">Například vlastnost `MyProp` může být svázána s `MyEventHandler` pomocí následujících dvou atributů:</span><span class="sxs-lookup"><span data-stu-id="16656-255">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="16656-256">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="16656-256">Event handling</span></span>

<span data-ttu-id="16656-257">Komponenty Razor poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="16656-257">Razor components provide event handling features.</span></span> <span data-ttu-id="16656-258">Pro atribut elementu HTML s názvem `on{event}` (například `onclick` a `onsubmit`) s hodnotou delegovanou pomocí komponenty Razor se hodnota atributu považuje za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="16656-258">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="16656-259">Název atributu má vždycky formát [@on {Event}](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="16656-259">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="16656-260">Následující kód volá metodu `UpdateHeading`, pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="16656-260">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="16656-261">Následující kód volá metodu `CheckChanged`, pokud je zaškrtávací políčko v uživatelském rozhraní změněno:</span><span class="sxs-lookup"><span data-stu-id="16656-261">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="16656-262">Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="16656-262">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="16656-263">Není nutné ručně volat `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="16656-263">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="16656-264">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="16656-264">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="16656-265">V následujícím příkladu je `UpdateHeading` volána asynchronně, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="16656-265">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="16656-266">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="16656-266">Event argument types</span></span>

<span data-ttu-id="16656-267">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="16656-267">For some events, event argument types are permitted.</span></span> <span data-ttu-id="16656-268">Pokud přístup k některému z těchto typů událostí není nezbytný, není nutné ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="16656-268">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="16656-269">Podporované `EventArgs` jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="16656-269">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="16656-270">Událost</span><span class="sxs-lookup"><span data-stu-id="16656-270">Event</span></span> | <span data-ttu-id="16656-271">Třída</span><span class="sxs-lookup"><span data-stu-id="16656-271">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="16656-272">Schránka</span><span class="sxs-lookup"><span data-stu-id="16656-272">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="16656-273">Přetažení</span><span class="sxs-lookup"><span data-stu-id="16656-273">Drag</span></span>             | <span data-ttu-id="16656-274">`DragEventArgs` &ndash; `DataTransfer` a `DataTransferItem` blokování dat přetažených položek.</span><span class="sxs-lookup"><span data-stu-id="16656-274">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="16656-275">Chyba</span><span class="sxs-lookup"><span data-stu-id="16656-275">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="16656-276">Vybrána</span><span class="sxs-lookup"><span data-stu-id="16656-276">Focus</span></span>            | <span data-ttu-id="16656-277">`FocusEventArgs` &ndash; nezahrnuje podporu pro `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="16656-277">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="16656-278">Změna `<input>`</span><span class="sxs-lookup"><span data-stu-id="16656-278">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="16656-279">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="16656-279">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="16656-280">Stisknut</span><span class="sxs-lookup"><span data-stu-id="16656-280">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="16656-281">Ukazatele myši</span><span class="sxs-lookup"><span data-stu-id="16656-281">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="16656-282">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="16656-282">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="16656-283">Průběh</span><span class="sxs-lookup"><span data-stu-id="16656-283">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="16656-284">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="16656-284">Touch</span></span>            | <span data-ttu-id="16656-285">`TouchEventArgs` &ndash; `TouchPoint` představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="16656-285">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="16656-286">Informace o vlastnostech a chování zpracování událostí událostí v předchozí tabulce naleznete v tématu [třídy EventArgs ve zdroji referencí (ASPNET/AspNetCore Release/3.0)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="16656-286">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="16656-287">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="16656-287">Lambda expressions</span></span>

<span data-ttu-id="16656-288">Lambda výrazy lze také použít:</span><span class="sxs-lookup"><span data-stu-id="16656-288">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="16656-289">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="16656-289">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="16656-290">Následující příklad vytvoří tři tlačítka, z nichž každá volá `UpdateHeading`, která předá argument události (`MouseEventArgs`) a číslo jeho tlačítka (`buttonNumber`), pokud je vybrána v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="16656-290">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="16656-291">Nepoužívejte **proměnnou** smyčky (`i`) ve smyčce `for` přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="16656-291">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="16656-292">Jinak je stejná proměnná používána všemi výrazy lambda, což způsobí, že hodnota `i` bude stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="16656-292">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="16656-293">Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="16656-293">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="16656-294">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="16656-294">EventCallback</span></span>

<span data-ttu-id="16656-295">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené komponenty @ no__t-0for, když dojde k události `onclick` v podřízeném objektu.</span><span class="sxs-lookup"><span data-stu-id="16656-295">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="16656-296">Chcete-li zobrazit události napříč komponentami, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="16656-296">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="16656-297">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="16656-297">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="16656-298">@No__t-0 v ukázkové aplikaci ukazuje, jak je nastavená obslužná rutina `onclick` tlačítka pro příjem delegáta `EventCallback` z `ParentComponent` ukázky.</span><span class="sxs-lookup"><span data-stu-id="16656-298">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="16656-299">@No__t-0 se zadává pomocí `MouseEventArgs`, který je vhodný pro událost `onclick` z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="16656-299">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="16656-300">@No__t-0 nastaví `EventCallback<T>` dítěte na jeho metodu `ShowMessage`:</span><span class="sxs-lookup"><span data-stu-id="16656-300">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="16656-301">Když je vybráno tlačítko v `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="16656-301">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="16656-302">Je volána metoda `ParentComponent` `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="16656-302">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="16656-303">`messageText` se aktualizuje a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="16656-303">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="16656-304">Volání metody `StateHasChanged` není vyžadováno v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="16656-304">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="16656-305">`StateHasChanged` se zavolá automaticky a znovu vykreslí `ParentComponent` stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách událostí, které se spouštějí v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="16656-305">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="16656-306">`EventCallback` a `EventCallback<T>` povolí asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="16656-306">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="16656-307">`EventCallback<T>` je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="16656-307">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="16656-308">`EventCallback` je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="16656-308">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="16656-309">Vyvolat `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a očekávat <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="16656-309">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="16656-310">Pro zpracování událostí a parametry komponenty vazby použijte `EventCallback` a `EventCallback<T>`.</span><span class="sxs-lookup"><span data-stu-id="16656-310">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="16656-311">Dáváte přednost silnému typu `EventCallback<T>` přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="16656-311">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="16656-312">`EventCallback<T>` poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-312">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="16656-313">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="16656-313">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="16656-314">Pokud zpětnému volání není předáno žádné číslo, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="16656-314">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="chained-bind"></a><span data-ttu-id="16656-315">Zřetězená vazba</span><span class="sxs-lookup"><span data-stu-id="16656-315">Chained bind</span></span>

<span data-ttu-id="16656-316">Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-316">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="16656-317">Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.</span><span class="sxs-lookup"><span data-stu-id="16656-317">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="16656-318">Zřetězenou BIND nelze implementovat s syntaxí `@bind` v elementu stránky.</span><span class="sxs-lookup"><span data-stu-id="16656-318">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="16656-319">Obslužná rutina události a hodnota se musí zadat samostatně.</span><span class="sxs-lookup"><span data-stu-id="16656-319">The event handler and value must be specified separately.</span></span> <span data-ttu-id="16656-320">Nadřazená komponenta však může použít syntaxi `@bind` s parametrem součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-320">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="16656-321">Následující součást `PasswordField` (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="16656-321">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="16656-322">Nastaví hodnotu elementu `<input>` na vlastnost `Password`.</span><span class="sxs-lookup"><span data-stu-id="16656-322">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="16656-323">Zveřejňuje změny vlastnosti `Password` u nadřazené komponenty pomocí [vnořenou eventCallback](#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="16656-323">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="16656-324">Komponenta `PasswordField` se používá v jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="16656-324">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="16656-325">Chcete-li provést kontrolu nebo chyby depeše v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="16656-325">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="16656-326">Vytvořte pole pro zálohování pro `Password` (`password` v následujícím ukázkovém kódu).</span><span class="sxs-lookup"><span data-stu-id="16656-326">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="16656-327">Proveďte kontroly nebo chyby depeší v metodě setter `Password`.</span><span class="sxs-lookup"><span data-stu-id="16656-327">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="16656-328">Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:</span><span class="sxs-lookup"><span data-stu-id="16656-328">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="16656-329">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="16656-329">Capture references to components</span></span>

<span data-ttu-id="16656-330">Odkazy na součásti poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="16656-330">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="16656-331">Zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="16656-331">To capture a component reference:</span></span>

* <span data-ttu-id="16656-332">Přidejte atribut [@ref](xref:mvc/views/razor#ref) do podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-332">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="16656-333">Definujte pole stejného typu jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="16656-333">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="16656-334">Při vykreslení komponenty se v poli `loginDialog` naplní instance podřízené komponenty `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="16656-334">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="16656-335">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="16656-335">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="16656-336">Proměnná `loginDialog` je naplněna pouze po vykreslení komponenty a její výstup obsahuje prvek `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="16656-336">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="16656-337">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="16656-337">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="16656-338">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte metody `OnAfterRenderAsync` nebo `OnAfterRender`.</span><span class="sxs-lookup"><span data-stu-id="16656-338">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="16656-339">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není to funkce [interoperability JavaScriptu](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="16656-339">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="16656-340">Odkazy na součásti nejsou předány kódu JavaScriptu @ no__t-0they're, který se používá pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="16656-340">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="16656-341">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="16656-341">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="16656-342">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="16656-342">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="16656-343">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="16656-343">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="16656-344">Vyvolat metody komponenty externě na stav aktualizace</span><span class="sxs-lookup"><span data-stu-id="16656-344">Invoke component methods externally to update state</span></span>

<span data-ttu-id="16656-345">Blazor používá `SynchronizationContext` k vykonání jednoho logického vlákna provádění.</span><span class="sxs-lookup"><span data-stu-id="16656-345">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="16656-346">Metody životního cyklu komponenty a všechna zpětná volání událostí, která jsou aktivována nástrojem Blazor, jsou spouštěna v tomto `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="16656-346">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="16656-347">V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte metodu `InvokeAsync`, která se odešle do `SynchronizationContext` Blazor.</span><span class="sxs-lookup"><span data-stu-id="16656-347">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="16656-348">Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:</span><span class="sxs-lookup"><span data-stu-id="16656-348">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="16656-349">Použití `NotifierService` k aktualizaci součásti:</span><span class="sxs-lookup"><span data-stu-id="16656-349">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="16656-350">V předchozím příkladu `NotifierService` vyvolá metodu `OnNotify` komponenty mimo Blazor `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="16656-350">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="16656-351">`InvokeAsync` slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.</span><span class="sxs-lookup"><span data-stu-id="16656-351">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="16656-352">Použití \@key k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="16656-352">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="16656-353">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazor rozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="16656-353">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="16656-354">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="16656-354">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="16656-355">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="16656-355">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="16656-356">Obsah kolekce `People` se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="16656-356">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="16656-357">Když se komponenta znovu vykreslí, součást `<DetailsEditor>` se může změnit, aby přijímala různé hodnoty parametrů `Details`.</span><span class="sxs-lookup"><span data-stu-id="16656-357">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="16656-358">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="16656-358">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="16656-359">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="16656-359">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="16656-360">Proces mapování lze řídit pomocí atributu direktivy `@key`.</span><span class="sxs-lookup"><span data-stu-id="16656-360">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="16656-361">`@key` způsobí, že rozdílový algoritmus zaručuje zachování prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="16656-361">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="16656-362">Když se změní kolekce `People`, rozdílový algoritmus zachová přidružení mezi instancemi `<DetailsEditor>` a instancemi `person`:</span><span class="sxs-lookup"><span data-stu-id="16656-362">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="16656-363">Pokud se ze seznamu `People` odstraní `Person`, z uživatelského rozhraní se odebere jenom odpovídající instance `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="16656-363">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="16656-364">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="16656-364">Other instances are left unchanged.</span></span>
* <span data-ttu-id="16656-365">Pokud je na některém místě v seznamu vložená `Person`, do příslušné pozice se vloží jedna nová instance `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="16656-365">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="16656-366">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="16656-366">Other instances are left unchanged.</span></span>
* <span data-ttu-id="16656-367">Pokud jsou položky `Person` znovu objednány, odpovídající instance `<DetailsEditor>` jsou zachovány a znovu uspořádány v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="16656-367">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="16656-368">V některých scénářích použití `@key` minimalizuje složitost opětovného vykreslování a vyhnete se potenciálním problémům se měnícími se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="16656-368">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="16656-369">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="16656-369">Keys are local to each container element or component.</span></span> <span data-ttu-id="16656-370">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="16656-370">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="16656-371">Kdy použít @no__t – 0key</span><span class="sxs-lookup"><span data-stu-id="16656-371">When to use \@key</span></span>

<span data-ttu-id="16656-372">Obvykle má smysl použít `@key` při každém vykreslení seznamu (například v bloku `@foreach`) a existuje vhodná hodnota pro definování `@key`.</span><span class="sxs-lookup"><span data-stu-id="16656-372">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="16656-373">@No__t-0 můžete také použít, chcete-li zabránit Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="16656-373">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="16656-374">Pokud se změní `@currentPerson`, direktiva Attribute `@key` vynutí, aby Blazor zahození celého `<div>` a jeho následníků a znovu sestaví podstrom v uživatelském rozhraní s novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="16656-374">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="16656-375">To může být užitečné, pokud potřebujete zaručit, že při změně `@currentPerson` se nezachová žádný stav uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="16656-375">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="16656-376">Kdy použít @no__t – 0key</span><span class="sxs-lookup"><span data-stu-id="16656-376">When not to use \@key</span></span>

<span data-ttu-id="16656-377">Při rozdílech s `@key` se účtují náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="16656-377">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="16656-378">Náklady na výkon nejsou velké, ale zadávejte pouze `@key`, pokud řízení pravidel uchovávání prvků nebo komponent těží z aplikace.</span><span class="sxs-lookup"><span data-stu-id="16656-378">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="16656-379">I v případě, že `@key` se nepoužívá, Blazor zachová podřízený element a instance komponenty co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="16656-379">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="16656-380">Jedinou výhodou použití `@key` je řízení způsobu, *jakým* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="16656-380">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="16656-381">Jaké hodnoty se mají použít pro \@key</span><span class="sxs-lookup"><span data-stu-id="16656-381">What values to use for \@key</span></span>

<span data-ttu-id="16656-382">Obecně dává smysl, aby jeden z následujících typů hodnot `@key`:</span><span class="sxs-lookup"><span data-stu-id="16656-382">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="16656-383">Instance objektů modelu (například instance `Person` jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="16656-383">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="16656-384">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="16656-384">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="16656-385">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string` nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="16656-385">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="16656-386">Zajistěte, aby hodnoty používané pro `@key` nekolidují.</span><span class="sxs-lookup"><span data-stu-id="16656-386">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="16656-387">Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty střetu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo komponenty na nové prvky nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="16656-387">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="16656-388">Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="16656-388">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="16656-389">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="16656-389">Lifecycle methods</span></span>

<span data-ttu-id="16656-390">`OnInitializedAsync` a `OnInitialized` spustí kód pro inicializaci komponenty.</span><span class="sxs-lookup"><span data-stu-id="16656-390">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="16656-391">K provedení asynchronní operace použijte `OnInitializedAsync` a klíčové slovo `await` pro operaci:</span><span class="sxs-lookup"><span data-stu-id="16656-391">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="16656-392">Pro synchronní operaci použijte `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="16656-392">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="16656-393">`OnParametersSetAsync` a `OnParametersSet` jsou volány, když komponenta přijímá parametry z nadřazené položky a hodnoty jsou přiřazeny vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="16656-393">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="16656-394">Tyto metody jsou spouštěny po inicializaci komponenty a pokaždé, když je vykreslena komponenta:</span><span class="sxs-lookup"><span data-stu-id="16656-394">These methods are executed after component initialization and each time the component is rendered:</span></span>

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

<span data-ttu-id="16656-395">`OnAfterRenderAsync` a `OnAfterRender` jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-395">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="16656-396">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="16656-396">Element and component references are populated at this point.</span></span> <span data-ttu-id="16656-397">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="16656-397">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="16656-398">`OnAfterRender` *se nevolá při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="16656-398">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="16656-399">Parametr `firstRender` pro `OnAfterRenderAsync` a `OnAfterRender` je:</span><span class="sxs-lookup"><span data-stu-id="16656-399">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="16656-400">Nastavte na `true` při prvním vyvolání instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="16656-400">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="16656-401">Zajistí, že se práce s inicializací provádí jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="16656-401">Ensures that initialization work is only performed once.</span></span>

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

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="16656-402">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="16656-402">Handle incomplete async actions at render</span></span>

<span data-ttu-id="16656-403">Asynchronní akce provedené v událostech životního cyklu se možná nedokončily, než se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="16656-403">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="16656-404">Objekty mohou být `null` nebo kompletně naplněna daty při provádění metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="16656-404">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="16656-405">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="16656-405">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="16656-406">Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), zatímco objekty jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="16656-406">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="16656-407">V komponentě `FetchData` v šablonách Blazor `OnInitializedAsync` bude přepsáno, aby asynchronně přijímat data předpovědi (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="16656-407">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="16656-408">Pokud je `forecasts` `null`, uživateli se zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="16656-408">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="16656-409">Po dokončení `Task` vráceného `OnInitializedAsync` se komponenta znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="16656-409">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="16656-410">*Stránky/FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="16656-410">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="16656-411">Spustit kód před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="16656-411">Execute code before parameters are set</span></span>

<span data-ttu-id="16656-412">`SetParameters` může být přepsáno, aby se spustil kód před nastavením parametrů:</span><span class="sxs-lookup"><span data-stu-id="16656-412">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="16656-413">Pokud není vyvoláno `base.SetParameters`, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="16656-413">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="16656-414">Například příchozí parametry nemusejí být přiřazeny vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="16656-414">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="16656-415">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="16656-415">Suppress refreshing of the UI</span></span>

<span data-ttu-id="16656-416">`ShouldRender` se dá přepsat, aby se potlačila aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="16656-416">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="16656-417">Pokud implementace vrátí `true`, uživatelské rozhraní se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="16656-417">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="16656-418">I když je přepsána hodnota `ShouldRender`, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="16656-418">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="16656-419">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="16656-419">Component disposal with IDisposable</span></span>

<span data-ttu-id="16656-420">Pokud komponenta implementuje <xref:System.IDisposable>, [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) se zavolá, když se komponenta odebere z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="16656-420">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="16656-421">Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="16656-421">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

## <a name="routing"></a><span data-ttu-id="16656-422">Směrování</span><span class="sxs-lookup"><span data-stu-id="16656-422">Routing</span></span>

<span data-ttu-id="16656-423">Směrování v Blazor se dosahuje tak, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="16656-423">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="16656-424">Když je zkompilován soubor Razor s direktivou `@page`, vygenerovaná Třída předává <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="16656-424">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="16656-425">V době běhu Směrovač vyhledává třídy komponent s `RouteAttribute` a vykreslí, že každá komponenta má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="16656-425">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="16656-426">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="16656-426">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="16656-427">Následující komponenta reaguje na požadavky na `/BlazorRoute` a `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="16656-427">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="16656-428">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="16656-428">Route parameters</span></span>

<span data-ttu-id="16656-429">Komponenty mohou přijímat parametry tras z šablony trasy uvedené v direktivě `@page`.</span><span class="sxs-lookup"><span data-stu-id="16656-429">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="16656-430">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="16656-430">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="16656-431">*Komponenta parametru směrování*:</span><span class="sxs-lookup"><span data-stu-id="16656-431">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="16656-432">Volitelné parametry nejsou podporované, takže se v předchozím příkladu použijí dvě direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="16656-432">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="16656-433">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="16656-433">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="16656-434">Druhá direktiva `@page` přijímá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.</span><span class="sxs-lookup"><span data-stu-id="16656-434">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="16656-435">Dědičnost základní třídy pro prostředí s kódem na pozadí</span><span class="sxs-lookup"><span data-stu-id="16656-435">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="16656-436">Soubory součástí kombinují značky HTML C# a zpracovávají kód ve stejném souboru.</span><span class="sxs-lookup"><span data-stu-id="16656-436">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="16656-437">Direktiva `@inherits` se dá použít k poskytování aplikací Blazor s prostředím "kódu" na pozadí, které odděluje značku komponenty od zpracování kódu.</span><span class="sxs-lookup"><span data-stu-id="16656-437">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="16656-438">[Ukázková aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`, k poskytnutí vlastností a metod součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-438">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="16656-439">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="16656-439">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="16656-440">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="16656-440">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="16656-441">Základní třída by měla být odvozena od `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="16656-441">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="16656-442">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="16656-442">Import components</span></span>

<span data-ttu-id="16656-443">Obor názvů komponenty, která je vytvořená pomocí Razor, je založen na (v pořadí podle priority):</span><span class="sxs-lookup"><span data-stu-id="16656-443">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="16656-444">[@namespace](xref:mvc/views/razor#namespace) označení v souboru Razor ( *. Razor*) značky (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="16656-444">[@namespace](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="16656-445">Projektový `RootNamespace` v souboru projektu (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="16656-445">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="16656-446">Název projektu, pořízený z názvu souboru projektu ( *. csproj*) a cesta z kořenového adresáře projektu ke komponentě.</span><span class="sxs-lookup"><span data-stu-id="16656-446">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="16656-447">Například rozhraní řeší *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="16656-447">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="16656-448">Komponenty následují C# pravidla vazeb názvů.</span><span class="sxs-lookup"><span data-stu-id="16656-448">Components follow C# name binding rules.</span></span> <span data-ttu-id="16656-449">V tomto příkladu komponenty `Index` jsou součástí oboru všechny komponenty:</span><span class="sxs-lookup"><span data-stu-id="16656-449">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="16656-450">Ve stejné složce *stránky*.</span><span class="sxs-lookup"><span data-stu-id="16656-450">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="16656-451">Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.</span><span class="sxs-lookup"><span data-stu-id="16656-451">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="16656-452">Komponenty definované v jiném oboru názvů se přenesou do rozsahu pomocí direktivy [@using](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="16656-452">Components defined in a different namespace are brought into scope using Razor's [@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="16656-453">Pokud je v *BlazorSample/Shared/* Folder jiná komponenta `NavMenu.razor`, součást se dá použít v `Index.razor` s následujícím příkazem `@using`:</span><span class="sxs-lookup"><span data-stu-id="16656-453">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="16656-454">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují direktivu [@using](xref:mvc/views/razor#using) :</span><span class="sxs-lookup"><span data-stu-id="16656-454">Components can also be referenced using their fully qualified names, which doesn't require the [@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="16656-455">Kvalifikace `global::` není podporována.</span><span class="sxs-lookup"><span data-stu-id="16656-455">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="16656-456">Import komponent s aliasem `using` příkazy-0 (například `@using Foo = Bar`) se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="16656-456">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="16656-457">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="16656-457">Partially qualified names aren't supported.</span></span> <span data-ttu-id="16656-458">Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` s `<Shared.NavMenu></Shared.NavMenu>` se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="16656-458">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="16656-459">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="16656-459">Conditional HTML element attributes</span></span>

<span data-ttu-id="16656-460">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="16656-460">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="16656-461">Pokud je hodnota `false` nebo `null`, atribut se nevykresluje.</span><span class="sxs-lookup"><span data-stu-id="16656-461">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="16656-462">Pokud je hodnota `true`, atribut se vykreslí jako minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="16656-462">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="16656-463">V následujícím příkladu `IsCompleted` určuje, zda je v označení prvku vykreslena `checked`:</span><span class="sxs-lookup"><span data-stu-id="16656-463">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="16656-464">Pokud je `IsCompleted` `true`, zaškrtávací políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="16656-464">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="16656-465">Pokud je `IsCompleted` `false`, zaškrtávací políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="16656-465">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="16656-466">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="16656-466">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="16656-467">Některé atributy HTML, jako je například [Standard ARIA](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nebudou fungovat správně, pokud je typ .NET `bool`.</span><span class="sxs-lookup"><span data-stu-id="16656-467">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="16656-468">V těchto případech použijte místo `bool` typ @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="16656-468">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="16656-469">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="16656-469">Raw HTML</span></span>

<span data-ttu-id="16656-470">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="16656-470">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="16656-471">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do hodnoty @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="16656-471">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="16656-472">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="16656-472">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="16656-473">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="16656-473">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="16656-474">Následující příklad ukazuje použití typu `MarkupString` pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="16656-474">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="16656-475">Komponenty se šablonami</span><span class="sxs-lookup"><span data-stu-id="16656-475">Templated components</span></span>

<span data-ttu-id="16656-476">Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="16656-476">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="16656-477">Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-477">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="16656-478">Mezi několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="16656-478">A couple of examples include:</span></span>

* <span data-ttu-id="16656-479">Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="16656-479">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="16656-480">Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="16656-480">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="16656-481">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="16656-481">Template parameters</span></span>

<span data-ttu-id="16656-482">Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="16656-482">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="16656-483">Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="16656-483">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="16656-484">`RenderFragment<T>` převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.</span><span class="sxs-lookup"><span data-stu-id="16656-484">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="16656-485">součást `TableTemplate`:</span><span class="sxs-lookup"><span data-stu-id="16656-485">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="16656-486">Při použití komponenty se šablonou lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="16656-486">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="pets">
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

### <a name="template-context-parameters"></a><span data-ttu-id="16656-487">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="16656-487">Template context parameters</span></span>

<span data-ttu-id="16656-488">Argumenty součásti typu `RenderFragment<T>` předané jako elementy mají implicitní parametr s názvem `context` (například z předchozí ukázky kódu `@context.PetId`), ale můžete změnit název parametru pomocí atributu `Context` v podřízeném elementu.</span><span class="sxs-lookup"><span data-stu-id="16656-488">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="16656-489">V následujícím příkladu atribut `Context` elementu `RowTemplate` určuje parametr `pet`:</span><span class="sxs-lookup"><span data-stu-id="16656-489">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="pets">
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

<span data-ttu-id="16656-490">Alternativně lze zadat atribut `Context` u prvku součásti.</span><span class="sxs-lookup"><span data-stu-id="16656-490">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="16656-491">Zadaný atribut `Context` se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="16656-491">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="16656-492">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu).</span><span class="sxs-lookup"><span data-stu-id="16656-492">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="16656-493">V následujícím příkladu se atribut `Context` zobrazuje u prvku `TableTemplate` a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="16656-493">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="pets" Context="pet">
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

### <a name="generic-typed-components"></a><span data-ttu-id="16656-494">Komponenty s obecným typem</span><span class="sxs-lookup"><span data-stu-id="16656-494">Generic-typed components</span></span>

<span data-ttu-id="16656-495">Komponenty se šablonami jsou často typu obecně typované.</span><span class="sxs-lookup"><span data-stu-id="16656-495">Templated components are often generically typed.</span></span> <span data-ttu-id="16656-496">Například obecná komponenta `ListViewTemplate` může být použita k vykreslování hodnot `IEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="16656-496">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="16656-497">Chcete-li definovat obecné komponenty, použijte direktivu [@typeparam](xref:mvc/views/razor#typeparam) pro určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="16656-497">To define a generic component, use the [@typeparam](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="16656-498">Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="16656-498">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="16656-499">V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="16656-499">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="16656-500">V následujícím příkladu `TItem="Pet"` určuje typ:</span><span class="sxs-lookup"><span data-stu-id="16656-500">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="16656-501">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="16656-501">Cascading values and parameters</span></span>

<span data-ttu-id="16656-502">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="16656-502">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="16656-503">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="16656-503">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="16656-504">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="16656-504">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="16656-505">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="16656-505">Theme example</span></span>

<span data-ttu-id="16656-506">V následujícím příkladu z ukázkové aplikace určuje třída `ThemeInfo` informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="16656-506">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="16656-507">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="16656-507">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="16656-508">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="16656-508">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="16656-509">Komponenta `CascadingValue` balí podstrom hierarchie komponent a poskytuje jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="16656-509">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="16656-510">Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří text rozložení vlastnosti `@Body`.</span><span class="sxs-lookup"><span data-stu-id="16656-510">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="16656-511">`ButtonClass` je v komponentě rozložení přiřazena hodnota `btn-success`.</span><span class="sxs-lookup"><span data-stu-id="16656-511">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="16656-512">Jakákoli odvozená komponenta může tuto vlastnost spotřebovat prostřednictvím kaskádového objektu `ThemeInfo`.</span><span class="sxs-lookup"><span data-stu-id="16656-512">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="16656-513">součást `CascadingValuesParametersLayout`:</span><span class="sxs-lookup"><span data-stu-id="16656-513">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
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

<span data-ttu-id="16656-514">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí atributu `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="16656-514">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="16656-515">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="16656-515">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="16656-516">V ukázkové aplikaci váže komponenta `CascadingValuesParametersTheme` kaskádovou hodnotu `ThemeInfo` k kaskádovým parametrům.</span><span class="sxs-lookup"><span data-stu-id="16656-516">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="16656-517">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="16656-517">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="16656-518">součást `CascadingValuesParametersTheme`:</span><span class="sxs-lookup"><span data-stu-id="16656-518">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="16656-519">Chcete-li do stejného podstromu přenést více hodnot stejného typu, zadejte jedinečný řetězec `Name` ke každé komponentě `CascadingValue` a odpovídající `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="16656-519">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="16656-520">V následujícím příkladu jsou dvě komponenty `CascadingValue` kaskádovitě odlišné instance `MyCascadingType` podle názvu:</span><span class="sxs-lookup"><span data-stu-id="16656-520">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="16656-521">V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="16656-521">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="16656-522">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="16656-522">TabSet example</span></span>

<span data-ttu-id="16656-523">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="16656-523">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="16656-524">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="16656-524">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="16656-525">Ukázková aplikace obsahuje rozhraní `ITab`, které karty implementují:</span><span class="sxs-lookup"><span data-stu-id="16656-525">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="16656-526">Komponenta `CascadingValuesParametersTabSet` používá komponentu `TabSet`, která obsahuje několik součástí `Tab`:</span><span class="sxs-lookup"><span data-stu-id="16656-526">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="16656-527">Podřízené součásti `Tab` nejsou explicitně předány jako parametry `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="16656-527">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="16656-528">Místo toho jsou podřízené součásti `Tab` součástí podřízeného obsahu `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="16656-528">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="16656-529">@No__t-0 však stále potřebuje znát každou součást `Tab`, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, komponenta `TabSet` *může poskytnout sebe sama jako kaskádovou hodnotu* , kterou pak vybraly podřízené komponenty `Tab`.</span><span class="sxs-lookup"><span data-stu-id="16656-529">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="16656-530">součást `TabSet`:</span><span class="sxs-lookup"><span data-stu-id="16656-530">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="16656-531">Podřízené komponenty `Tab` zachytí obsahující `TabSet` jako kaskádový parametr, takže součásti `Tab` přidají sebe sama do `TabSet` a koordinují, na které kartě je aktivní.</span><span class="sxs-lookup"><span data-stu-id="16656-531">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="16656-532">součást `Tab`:</span><span class="sxs-lookup"><span data-stu-id="16656-532">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="16656-533">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="16656-533">Razor templates</span></span>

<span data-ttu-id="16656-534">Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor.</span><span class="sxs-lookup"><span data-stu-id="16656-534">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="16656-535">Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="16656-535">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="16656-536">Následující příklad ukazuje, jak zadat hodnoty `RenderFragment` a `RenderFragment<T>` a vykreslit šablony přímo v komponentě.</span><span class="sxs-lookup"><span data-stu-id="16656-536">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="16656-537">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="16656-537">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="16656-538">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="16656-538">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="16656-539">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="16656-539">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="16656-540">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` poskytuje metody pro manipulaci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.</span><span class="sxs-lookup"><span data-stu-id="16656-540">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="16656-541">Použití `RenderTreeBuilder` pro vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="16656-541">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="16656-542">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="16656-542">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="16656-543">Vezměte v úvahu následující součást `PetDetails`, kterou je možné ručně vytvořit do jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="16656-543">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="16656-544">V následujícím příkladu smyčka v metodě `CreateComponent` generuje tři komponenty `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="16656-544">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="16656-545">Při volání metod `RenderTreeBuilder` pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="16656-545">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="16656-546">Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání.</span><span class="sxs-lookup"><span data-stu-id="16656-546">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="16656-547">Při vytváření komponenty s metodami `RenderTreeBuilder` nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="16656-547">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="16656-548">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="16656-548">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="16656-549">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="16656-549">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="16656-550">součást `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="16656-550">`BuiltContent` component:</span></span>

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

> <span data-ttu-id="16656-551">! Upozornění Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* operací vykreslování.</span><span class="sxs-lookup"><span data-stu-id="16656-551">![WARNING] The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="16656-552">Jedná se o interní podrobnosti implementace Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="16656-552">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="16656-553">Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.</span><span class="sxs-lookup"><span data-stu-id="16656-553">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="16656-554">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="16656-554">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="16656-555">Soubory Blazor `.razor` jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="16656-555">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="16656-556">To je potenciálně skvělý přínos pro `.razor`, protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="16656-556">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="16656-557">Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*.</span><span class="sxs-lookup"><span data-stu-id="16656-557">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="16656-558">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="16656-558">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="16656-559">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="16656-559">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="16656-560">Vezměte v úvahu následující jednoduchý soubor `.razor`:</span><span class="sxs-lookup"><span data-stu-id="16656-560">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="16656-561">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="16656-561">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="16656-562">Při prvním spuštění kódu, pokud `someFlag` je `true`, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="16656-562">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="16656-563">Pořadí</span><span class="sxs-lookup"><span data-stu-id="16656-563">Sequence</span></span> | <span data-ttu-id="16656-564">type</span><span class="sxs-lookup"><span data-stu-id="16656-564">Type</span></span>      | <span data-ttu-id="16656-565">Data</span><span class="sxs-lookup"><span data-stu-id="16656-565">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="16656-566">0</span><span class="sxs-lookup"><span data-stu-id="16656-566">0</span></span>        | <span data-ttu-id="16656-567">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="16656-567">Text node</span></span> | <span data-ttu-id="16656-568">První</span><span class="sxs-lookup"><span data-stu-id="16656-568">First</span></span>  |
| <span data-ttu-id="16656-569">1</span><span class="sxs-lookup"><span data-stu-id="16656-569">1</span></span>        | <span data-ttu-id="16656-570">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="16656-570">Text node</span></span> | <span data-ttu-id="16656-571">Sekunda</span><span class="sxs-lookup"><span data-stu-id="16656-571">Second</span></span> |

<span data-ttu-id="16656-572">Představte si, že `someFlag` se naplní `false` a kód se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="16656-572">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="16656-573">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="16656-573">This time, the builder receives:</span></span>

| <span data-ttu-id="16656-574">Pořadí</span><span class="sxs-lookup"><span data-stu-id="16656-574">Sequence</span></span> | <span data-ttu-id="16656-575">type</span><span class="sxs-lookup"><span data-stu-id="16656-575">Type</span></span>       | <span data-ttu-id="16656-576">Data</span><span class="sxs-lookup"><span data-stu-id="16656-576">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="16656-577">1</span><span class="sxs-lookup"><span data-stu-id="16656-577">1</span></span>        | <span data-ttu-id="16656-578">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="16656-578">Text node</span></span>  | <span data-ttu-id="16656-579">Sekunda</span><span class="sxs-lookup"><span data-stu-id="16656-579">Second</span></span> |

<span data-ttu-id="16656-580">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci @no__t 0 byla odebrána, takže generuje následující skript triviálního *úprav*:</span><span class="sxs-lookup"><span data-stu-id="16656-580">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="16656-581">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="16656-581">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="16656-582">Co je špatné, pokud generujete pořadová čísla programově</span><span class="sxs-lookup"><span data-stu-id="16656-582">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="16656-583">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="16656-583">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="16656-584">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="16656-584">Now, the first output is:</span></span>

| <span data-ttu-id="16656-585">Pořadí</span><span class="sxs-lookup"><span data-stu-id="16656-585">Sequence</span></span> | <span data-ttu-id="16656-586">type</span><span class="sxs-lookup"><span data-stu-id="16656-586">Type</span></span>      | <span data-ttu-id="16656-587">Data</span><span class="sxs-lookup"><span data-stu-id="16656-587">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="16656-588">0</span><span class="sxs-lookup"><span data-stu-id="16656-588">0</span></span>        | <span data-ttu-id="16656-589">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="16656-589">Text node</span></span> | <span data-ttu-id="16656-590">První</span><span class="sxs-lookup"><span data-stu-id="16656-590">First</span></span>  |
| <span data-ttu-id="16656-591">1</span><span class="sxs-lookup"><span data-stu-id="16656-591">1</span></span>        | <span data-ttu-id="16656-592">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="16656-592">Text node</span></span> | <span data-ttu-id="16656-593">Sekunda</span><span class="sxs-lookup"><span data-stu-id="16656-593">Second</span></span> |

<span data-ttu-id="16656-594">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="16656-594">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="16656-595">`someFlag` je ve druhém vykreslování `false` a výstup je následující:</span><span class="sxs-lookup"><span data-stu-id="16656-595">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="16656-596">Pořadí</span><span class="sxs-lookup"><span data-stu-id="16656-596">Sequence</span></span> | <span data-ttu-id="16656-597">type</span><span class="sxs-lookup"><span data-stu-id="16656-597">Type</span></span>      | <span data-ttu-id="16656-598">Data</span><span class="sxs-lookup"><span data-stu-id="16656-598">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="16656-599">0</span><span class="sxs-lookup"><span data-stu-id="16656-599">0</span></span>        | <span data-ttu-id="16656-600">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="16656-600">Text node</span></span> | <span data-ttu-id="16656-601">Sekunda</span><span class="sxs-lookup"><span data-stu-id="16656-601">Second</span></span> |

<span data-ttu-id="16656-602">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="16656-602">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="16656-603">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="16656-603">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="16656-604">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="16656-604">Remove the second text node.</span></span>

<span data-ttu-id="16656-605">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde se v původním kódu objevily větve `if/else` a smyčky.</span><span class="sxs-lookup"><span data-stu-id="16656-605">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="16656-606">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="16656-606">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="16656-607">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="16656-607">This is a trivial example.</span></span> <span data-ttu-id="16656-608">Ve složitějších případech se složitými a hluboce vnořenými strukturami, a to hlavně pomocí smyček, náklady na výkon jsou závažnější.</span><span class="sxs-lookup"><span data-stu-id="16656-608">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="16656-609">Místo toho, aby se ihned identifikovaly, které bloky nebo větve smyček byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování a obvykle sestavovat mnohem delší úpravu skriptů, protože je nepřehledně neinformován o tom, jak staré a nové struktury vzájemná vazba.</span><span class="sxs-lookup"><span data-stu-id="16656-609">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="16656-610">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="16656-610">Guidance and conclusions</span></span>

* <span data-ttu-id="16656-611">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="16656-611">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="16656-612">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="16656-612">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="16656-613">Nepište dlouhé bloky ručně implementovaného `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="16656-613">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="16656-614">Preferovat `.razor` souborů a umožňuje kompilátoru zabývat se čísly sekvence.</span><span class="sxs-lookup"><span data-stu-id="16656-614">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="16656-615">Pokud se nemůžete vyhnout ruční logice `RenderTreeBuilder`, rozdělte dlouhé bloky kódu na menší části zabalené v `OpenRegion` @ no__t-2 @ no__t-3 volání.</span><span class="sxs-lookup"><span data-stu-id="16656-615">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="16656-616">Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).</span><span class="sxs-lookup"><span data-stu-id="16656-616">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="16656-617">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="16656-617">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="16656-618">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="16656-618">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="16656-619">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="16656-619">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="16656-620">Blazor používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="16656-620">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="16656-621">Rozdíly jsou mnohem rychlejší při použití pořadových čísel a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře vytvářející `.razor` souborů.</span><span class="sxs-lookup"><span data-stu-id="16656-621">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="16656-622">Lokalizace</span><span class="sxs-lookup"><span data-stu-id="16656-622">Localization</span></span>

<span data-ttu-id="16656-623">Aplikace Blazor serveru jsou lokalizovány pomocí [middleware pro lokalizaci](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="16656-623">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="16656-624">Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.</span><span class="sxs-lookup"><span data-stu-id="16656-624">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="16656-625">Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="16656-625">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="16656-626">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="16656-626">Cookies</span></span>](#cookies)
* [<span data-ttu-id="16656-627">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="16656-627">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="16656-628">Další informace a příklady najdete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="16656-628">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="16656-629">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="16656-629">Cookies</span></span>

<span data-ttu-id="16656-630">Soubor cookie lokalizační kultury může zachovat jazykovou verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="16656-630">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="16656-631">Soubor cookie je vytvořen metodou `OnGet` stránky hostitele aplikace (*Pages/Host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="16656-631">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="16656-632">Middleware lokalizace přečte soubor cookie při následných požadavcích na nastavení jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="16656-632">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="16656-633">Použití souboru cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="16656-633">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="16656-634">Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="16656-634">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="16656-635">Proto je doporučený přístup použití souboru cookie lokalizační kultury.</span><span class="sxs-lookup"><span data-stu-id="16656-635">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="16656-636">Pokud je jazyková verze uložena v souboru cookie lokalizace, je možné použít jakoukoli techniku k přiřazení jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="16656-636">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="16656-637">Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte soubor cookie lokalizační kultury v rámci schématu aplikace.</span><span class="sxs-lookup"><span data-stu-id="16656-637">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="16656-638">Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze přečíst pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="16656-638">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="16656-639">Vytvořte soubor *Pages/Host. cshtml. cs* s následujícím obsahem v aplikaci Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="16656-639">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="16656-640">Lokalizace se zpracovává v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="16656-640">Localization is handled in the app:</span></span>

1. <span data-ttu-id="16656-641">Prohlížeč pošle do aplikace počáteční požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="16656-641">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="16656-642">Jazyková verze je přiřazena pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="16656-642">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="16656-643">Metoda `OnGet` v *_Host. cshtml. cs* uchovává v rámci odpovědi jazykovou verzi v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="16656-643">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="16656-644">Prohlížeč otevře připojení pomocí protokolu WebSocket a vytvoří interaktivní relaci serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="16656-644">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="16656-645">Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="16656-645">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="16656-646">Relace serveru Blazor začíná správnou jazykovou verzí.</span><span class="sxs-lookup"><span data-stu-id="16656-646">The Blazor Server session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="16656-647">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="16656-647">Provide UI to choose the culture</span></span>

<span data-ttu-id="16656-648">K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* .</span><span class="sxs-lookup"><span data-stu-id="16656-648">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="16656-649">Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku. uživatel @ no__t-0the se přesměruje na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek.</span><span class="sxs-lookup"><span data-stu-id="16656-649">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="16656-650">Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler.</span><span class="sxs-lookup"><span data-stu-id="16656-650">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="16656-651">Řadič Nastaví vybranou jazykovou verzi uživatele na soubor cookie a přesměruje uživatele zpět na původní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="16656-651">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="16656-652">Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v souboru cookie a proveďte přesměrování zpátky na původní identifikátor URI:</span><span class="sxs-lookup"><span data-stu-id="16656-652">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="16656-653">Pomocí akce `LocalRedirect` zabráníte útokům v otevřeném přesměrování.</span><span class="sxs-lookup"><span data-stu-id="16656-653">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="16656-654">Další informace naleznete v tématu <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="16656-654">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="16656-655">Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:</span><span class="sxs-lookup"><span data-stu-id="16656-655">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

    private void OnSelected(ChangeEventArgs e)
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

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="16656-656">Použití scénářů lokalizace .NET v aplikacích Blazor</span><span class="sxs-lookup"><span data-stu-id="16656-656">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="16656-657">V aplikacích Blazor jsou k dispozici následující scénáře lokalizace a globalizace rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="16656-657">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="16656-658">. Systém prostředků netto</span><span class="sxs-lookup"><span data-stu-id="16656-658">.NET's resources system</span></span>
* <span data-ttu-id="16656-659">Formátování čísla a data specifické pro jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="16656-659">Culture-specific number and date formatting</span></span>

<span data-ttu-id="16656-660">Funkce Blazor `@bind` provádí globalizaci na základě aktuální jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="16656-660">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="16656-661">Další informace najdete v části [vázání dat](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="16656-661">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="16656-662">V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="16656-662">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="16656-663">@no__t – 0 *se* v aplikacích Blazor podporuje.</span><span class="sxs-lookup"><span data-stu-id="16656-663">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="16656-664">`IHtmlLocalizer<>`, `IViewLocalizer<>` a lokalizace datových poznámek jsou ASP.NET Core scénáře MVC a **nejsou podporovány** v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="16656-664">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="16656-665">Další informace naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="16656-665">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="16656-666">Obrázky ve formátu SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="16656-666">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="16656-667">Vzhledem k tomu, že Blazor vykresluje HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) ( *. SVG*), jsou podporovány prostřednictvím značky `<img>`:</span><span class="sxs-lookup"><span data-stu-id="16656-667">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="16656-668">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů ( *. CSS*):</span><span class="sxs-lookup"><span data-stu-id="16656-668">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="16656-669">Vložené značky SVG se však ve všech scénářích nepodporují.</span><span class="sxs-lookup"><span data-stu-id="16656-669">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="16656-670">Pokud značku `<svg>` umístíte přímo do souboru komponenty ( *. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů se ještě nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="16656-670">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="16656-671">Například značky `<use>` nejsou aktuálně dodrženy a `@bind` nelze použít s některými značkami SVG.</span><span class="sxs-lookup"><span data-stu-id="16656-671">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="16656-672">Očekáváme, že tato omezení vyřešíme v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="16656-672">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16656-673">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="16656-673">Additional resources</span></span>

* <span data-ttu-id="16656-674"><xref:security/blazor/server> &ndash; obsahuje pokyny k vytváření aplikací serveru Blazor, které musí soupeří s vyčerpáním prostředků.</span><span class="sxs-lookup"><span data-stu-id="16656-674"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
