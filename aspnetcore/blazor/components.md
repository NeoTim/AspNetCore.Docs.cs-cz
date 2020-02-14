---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 0da0d83a4fde7b753a84bf05d3a9284776f2881f
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213347"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="08b45-103">Vytváření a používání ASP.NET Corech komponent Razor</span><span class="sxs-lookup"><span data-stu-id="08b45-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="08b45-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="08b45-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="08b45-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="08b45-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="08b45-106">Aplikace Blazor jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="08b45-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="08b45-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="08b45-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="08b45-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="08b45-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="08b45-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="08b45-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="08b45-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="08b45-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="08b45-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="08b45-111">Component classes</span></span>

<span data-ttu-id="08b45-112">Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="08b45-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="08b45-113">Komponenta v Blazor je formálně označována jako *Komponenta Razor*.</span><span class="sxs-lookup"><span data-stu-id="08b45-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="08b45-114">Název součásti musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="08b45-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="08b45-115">Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.</span><span class="sxs-lookup"><span data-stu-id="08b45-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="08b45-116">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="08b45-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="08b45-117">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="08b45-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="08b45-118">Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti.</span><span class="sxs-lookup"><span data-stu-id="08b45-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="08b45-119">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="08b45-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="08b45-120">Členy třídy komponenty jsou definovány v `@code`ovém bloku.</span><span class="sxs-lookup"><span data-stu-id="08b45-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="08b45-121">V bloku `@code` je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="08b45-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="08b45-122">Je přípustný více než jeden blok `@code`.</span><span class="sxs-lookup"><span data-stu-id="08b45-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="08b45-123">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají na `@`.</span><span class="sxs-lookup"><span data-stu-id="08b45-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="08b45-124">Například C# pole se vykreslí pomocí předpony `@` názvu pole.</span><span class="sxs-lookup"><span data-stu-id="08b45-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="08b45-125">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="08b45-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="08b45-126">`_headingFontStyle` k hodnotě vlastnosti CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="08b45-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="08b45-127">`_headingText` k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="08b45-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="08b45-128">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="08b45-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="08b45-129">Blazor pak porovná nový strom vykreslování s předchozí a použije všechny změny v model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="08b45-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="08b45-130">Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="08b45-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="08b45-131">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="08b45-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="08b45-132">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="08b45-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="08b45-133">Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="08b45-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="08b45-134">Pokud je kořenový obor názvů aplikace `BlazorApp` a komponenta `Counter` se nachází ve složce *stránky* :</span><span class="sxs-lookup"><span data-stu-id="08b45-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="08b45-135">Obor názvů součásti `Counter` je `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="08b45-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="08b45-136">Plně kvalifikovaný název typu komponenty je `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="08b45-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="08b45-137">Další informace naleznete v části [Import komponent](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="08b45-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="08b45-138">Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky buď do nadřazené komponenty, nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="08b45-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="08b45-139">Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je kořenový obor názvů aplikace `BlazorApp`:</span><span class="sxs-lookup"><span data-stu-id="08b45-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="08b45-140">Integrace součástí do aplikací Razor Pages a MVC</span><span class="sxs-lookup"><span data-stu-id="08b45-140">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="08b45-141">Komponenty Razor lze integrovat do aplikací Razor Pages a MVC.</span><span class="sxs-lookup"><span data-stu-id="08b45-141">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="08b45-142">Po vykreslení stránky nebo zobrazení mohou být komponenty předem vykresleny ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="08b45-142">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="08b45-143">Pokud chcete připravovat aplikaci Razor Pages nebo MVC k hostování komponent Razor, postupujte podle pokynů v části *integrace prvků Razor do Razor Pages a MVC Apps* v článku <xref:blazor/hosting-model-configuration#integrate-razor-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="08b45-143">To prepare a Razor Pages or MVC app to host Razor components, follow the guidance in the *Integrate Razor components into Razor Pages and MVC apps* section of the <xref:blazor/hosting-model-configuration#integrate-razor-components-into-razor-pages-and-mvc-apps> article.</span></span>

<span data-ttu-id="08b45-144">Při použití vlastní složky k uchování součástí aplikace přidejte obor názvů představující složku do stránky nebo zobrazení nebo do souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="08b45-144">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="08b45-145">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="08b45-145">In the following example:</span></span>

* <span data-ttu-id="08b45-146">Změňte `MyAppNamespace` na obor názvů aplikace.</span><span class="sxs-lookup"><span data-stu-id="08b45-146">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="08b45-147">Pokud se složka s názvem *Components* nepoužívá k ukládání součástí, změňte `Components` do složky, kde jsou umístěny součásti.</span><span class="sxs-lookup"><span data-stu-id="08b45-147">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```csharp
@using MyAppNamespace.Components
```

<span data-ttu-id="08b45-148">Soubor *_ViewImports. cshtml* je umístěný ve složce *pages* aplikace Razor Pages nebo ve složce *zobrazení* aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="08b45-148">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="08b45-149">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte pomocníka značky `Component`:</span><span class="sxs-lookup"><span data-stu-id="08b45-149">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="08b45-150">Typ parametru musí být serializovatelný jako JSON, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="08b45-150">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="08b45-151">Můžete například zadat hodnotu pro `IncrementAmount`, protože typ `IncrementAmount` je `int`, což je primitivní typ podporovaný serializátorem JSON.</span><span class="sxs-lookup"><span data-stu-id="08b45-151">For example, you can specify a value for `IncrementAmount` because the type of `IncrementAmount` is an `int`, which is a primitive type supported by the JSON serializer.</span></span>

<span data-ttu-id="08b45-152">`RenderMode` nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="08b45-152">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="08b45-153">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="08b45-153">Is prerendered into the page.</span></span>
* <span data-ttu-id="08b45-154">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="08b45-154">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="08b45-155">Popis</span><span class="sxs-lookup"><span data-stu-id="08b45-155">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="08b45-156">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="08b45-156">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="08b45-157">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="08b45-157">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="08b45-158">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="08b45-158">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="08b45-159">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="08b45-159">Output from the component isn't included.</span></span> <span data-ttu-id="08b45-160">Po spuštění agenta uživatele se tato značka používá ke spuštění aplikace v Blazor.</span><span class="sxs-lookup"><span data-stu-id="08b45-160">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="08b45-161">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="08b45-161">Renders the component into static HTML.</span></span> |

<span data-ttu-id="08b45-162">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="08b45-162">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="08b45-163">Komponenty nemůžou používat scénáře zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="08b45-163">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="08b45-164">Chcete-li použít logiku z částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-164">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="08b45-165">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="08b45-165">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="08b45-166">Další informace o vykreslování komponent, stavu komponent a pomocníka značek `Component` naleznete v následujících článcích:</span><span class="sxs-lookup"><span data-stu-id="08b45-166">For more information on how components are rendered, component state, and the `Component` Tag Helper, see the following articles:</span></span>

* <xref:blazor/hosting-models>
* <xref:blazor/hosting-model-configuration>

## <a name="tag-helpers-arent-used-in-components"></a><span data-ttu-id="08b45-167">V součástech se nepoužívají pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="08b45-167">Tag Helpers aren't used in components</span></span>

<span data-ttu-id="08b45-168">V součástech Razor (soubory *. Razor* ) nejsou podporované [pomocníky značek](xref:mvc/views/tag-helpers/intro) .</span><span class="sxs-lookup"><span data-stu-id="08b45-168">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="08b45-169">Chcete-li v Blazor poskytnout funkce podobné pomocníkům, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-169">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="08b45-170">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="08b45-170">Use components</span></span>

<span data-ttu-id="08b45-171">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="08b45-171">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="08b45-172">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-172">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="08b45-173">Vazba atributu rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="08b45-173">Attribute binding is case sensitive.</span></span> <span data-ttu-id="08b45-174">Například `@bind` je platný a `@Bind` je neplatný.</span><span class="sxs-lookup"><span data-stu-id="08b45-174">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="08b45-175">Následující kód v *indexu. Razor* vykresluje instanci `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="08b45-175">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="08b45-176">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-176">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="08b45-177">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="08b45-177">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="08b45-178">Přidání příkazu `@using` pro obor názvů součásti zpřístupňuje komponentu, což odstraní upozornění.</span><span class="sxs-lookup"><span data-stu-id="08b45-178">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="08b45-179">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="08b45-179">Component parameters</span></span>

<span data-ttu-id="08b45-180">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="08b45-180">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="08b45-181">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="08b45-181">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="08b45-182">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-182">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="08b45-183">V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu vlastnosti `Title` `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="08b45-183">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="08b45-184">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-184">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a><span data-ttu-id="08b45-185">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="08b45-185">Child content</span></span>

<span data-ttu-id="08b45-186">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="08b45-186">Components can set the content of another component.</span></span> <span data-ttu-id="08b45-187">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-187">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="08b45-188">V následujícím příkladu má `ChildComponent` vlastnost `ChildContent`, která představuje `RenderFragment`, která představuje segment uživatelského rozhraní pro vykreslení.</span><span class="sxs-lookup"><span data-stu-id="08b45-188">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="08b45-189">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="08b45-189">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="08b45-190">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v `panel-body`panelu Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="08b45-190">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="08b45-191">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-191">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="08b45-192">Vlastnost, která přijímá obsah `RenderFragment`, musí mít název `ChildContent` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="08b45-192">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="08b45-193">`ParentComponent` v ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu do značek `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="08b45-193">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="08b45-194">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-194">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="08b45-195">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="08b45-195">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="08b45-196">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="08b45-196">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="08b45-197">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí direktivy [`@attributes`](xref:mvc/views/razor#attributes) Razor.</span><span class="sxs-lookup"><span data-stu-id="08b45-197">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="08b45-198">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="08b45-198">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="08b45-199">Například může být zdlouhavé definovat atributy samostatně pro `<input>`, které podporují mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="08b45-199">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="08b45-200">V následujícím příkladu první `<input>` element (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="08b45-200">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="08b45-201">Typ parametru musí implementovat `IEnumerable<KeyValuePair<string, object>>` s klíči řetězce.</span><span class="sxs-lookup"><span data-stu-id="08b45-201">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="08b45-202">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="08b45-202">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="08b45-203">Vykreslené `<input>` prvky pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="08b45-203">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="08b45-204">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí atributu `[Parameter]` s vlastností `CaptureUnmatchedValues` nastavenou na `true`:</span><span class="sxs-lookup"><span data-stu-id="08b45-204">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="08b45-205">Vlastnost `CaptureUnmatchedValues` v `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="08b45-205">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="08b45-206">Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="08b45-206">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="08b45-207">Typ vlastnosti používaný u `CaptureUnmatchedValues` musí být možné přiřadit z `Dictionary<string, object>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="08b45-207">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="08b45-208">v tomto scénáři jsou také možnosti `IEnumerable<KeyValuePair<string, object>>` nebo `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="08b45-208">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="08b45-209">Pozice `@attributes` relativní k pozici atributů elementu je důležitá.</span><span class="sxs-lookup"><span data-stu-id="08b45-209">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="08b45-210">Když `@attributes` jsou splattedy na elementu, atributy jsou zpracovávány zprava doleva (poslední až první).</span><span class="sxs-lookup"><span data-stu-id="08b45-210">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="08b45-211">Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:</span><span class="sxs-lookup"><span data-stu-id="08b45-211">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="08b45-212">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-212">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="08b45-213">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-213">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="08b45-214">Atribut `extra` `Child` komponenty je nastaven na hodnotu napravo od `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="08b45-214">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="08b45-215">`<div>` vykreslená komponenta `Parent` obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):</span><span class="sxs-lookup"><span data-stu-id="08b45-215">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="08b45-216">V následujícím příkladu je pořadí `extra` a `@attributes` v `<div>``Child` komponenty obrácené:</span><span class="sxs-lookup"><span data-stu-id="08b45-216">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="08b45-217">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="08b45-218">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-218">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="08b45-219">Vykreslený `<div>` v komponentě `Parent` obsahuje `extra="10"` při předání prostřednictvím dalšího atributu:</span><span class="sxs-lookup"><span data-stu-id="08b45-219">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="08b45-220">Vytváření datových vazeb</span><span class="sxs-lookup"><span data-stu-id="08b45-220">Data binding</span></span>

<span data-ttu-id="08b45-221">Datové vazby na součásti a prvky modelu DOM jsou provedeny atributem [`@bind`](xref:mvc/views/razor#bind) .</span><span class="sxs-lookup"><span data-stu-id="08b45-221">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="08b45-222">Následující příklad váže vlastnost `CurrentValue` k hodnotě v textovém poli:</span><span class="sxs-lookup"><span data-stu-id="08b45-222">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="08b45-223">Pokud textové pole ztratí fokus, je hodnota vlastnosti aktualizována.</span><span class="sxs-lookup"><span data-stu-id="08b45-223">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="08b45-224">Textové pole je aktualizováno v uživatelském rozhraní pouze v případě, že je komponenta vykreslena, nikoli v reakci na změnu hodnoty vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="08b45-224">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="08b45-225">Vzhledem k tomu, že se komponenty vykreslují po spuštění kódu obslužné rutiny události, se aktualizace vlastností *obvykle* projeví v uživatelském rozhraní hned po aktivaci obslužné rutiny události.</span><span class="sxs-lookup"><span data-stu-id="08b45-225">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="08b45-226">Použití `@bind` s vlastností `CurrentValue` (`<input @bind="CurrentValue" />`) je v podstatě ekvivalentem následujícího:</span><span class="sxs-lookup"><span data-stu-id="08b45-226">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="08b45-227">Při vykreslení komponenty se `value` vstupního elementu dostane z vlastnosti `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="08b45-227">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="08b45-228">Pokud uživatel zadá do textového pole a změní fokus prvku, je vyvolána událost `onchange` a vlastnost `CurrentValue` je nastavena na změněnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="08b45-228">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="08b45-229">Ve skutečnosti je generování kódu složitější, protože `@bind` zpracovává případy, kde jsou prováděny převody typů.</span><span class="sxs-lookup"><span data-stu-id="08b45-229">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="08b45-230">V zásadě `@bind` přidruží aktuální hodnotu výrazu k atributu `value` a zpracovává změny pomocí registrované obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="08b45-230">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="08b45-231">Kromě zpracování `onchange`ch událostí pomocí syntaxe `@bind` lze vlastnost nebo pole svázat pomocí jiných událostí zadáním atributu [`@bind-value`](xref:mvc/views/razor#bind) s parametrem `event` ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span><span class="sxs-lookup"><span data-stu-id="08b45-231">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="08b45-232">Následující příklad váže vlastnost `CurrentValue` pro událost `oninput`:</span><span class="sxs-lookup"><span data-stu-id="08b45-232">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="08b45-233">Na rozdíl od `onchange`, která je aktivována, když prvek ztratí fokus, `oninput` při změně hodnoty textového pole aktivována.</span><span class="sxs-lookup"><span data-stu-id="08b45-233">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="08b45-234">`@bind-value` v předchozím příkladu se váže:</span><span class="sxs-lookup"><span data-stu-id="08b45-234">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="08b45-235">Zadaný výraz (`CurrentValue`) k atributu `value` elementu.</span><span class="sxs-lookup"><span data-stu-id="08b45-235">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="08b45-236">Delegát události změny pro událost určenou v `@bind-value:event`.</span><span class="sxs-lookup"><span data-stu-id="08b45-236">A change event delegate to the event specified by `@bind-value:event`.</span></span>

### <a name="unparsable-values"></a><span data-ttu-id="08b45-237">Hodnoty, které nelze analyzovat</span><span class="sxs-lookup"><span data-stu-id="08b45-237">Unparsable values</span></span>

<span data-ttu-id="08b45-238">Když uživatel poskytne neanalyzovatelné hodnoty prvku DataBound, hodnota neanalyzovat se automaticky vrátí na předchozí hodnotu, když se aktivuje událost BIND.</span><span class="sxs-lookup"><span data-stu-id="08b45-238">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="08b45-239">Představte si následující scénář:</span><span class="sxs-lookup"><span data-stu-id="08b45-239">Consider the following scenario:</span></span>

* <span data-ttu-id="08b45-240">Element `<input>` je vázán na typ `int` s počáteční hodnotou `123`:</span><span class="sxs-lookup"><span data-stu-id="08b45-240">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="08b45-241">Uživatel aktualizuje hodnotu prvku tak, aby `123.45` na stránce a změnil fokus prvku.</span><span class="sxs-lookup"><span data-stu-id="08b45-241">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="08b45-242">V předchozím scénáři je hodnota elementu vrácena na `123`.</span><span class="sxs-lookup"><span data-stu-id="08b45-242">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="08b45-243">Pokud je hodnota `123.45` zamítnuta ve prospěch původní hodnoty `123`, uživateli se rozumí, že jejich hodnota nebyla přijata.</span><span class="sxs-lookup"><span data-stu-id="08b45-243">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="08b45-244">Ve výchozím nastavení se vazba vztahuje na událost `onchange` elementu (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="08b45-244">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="08b45-245">K nastavení jiné události použijte `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}`.</span><span class="sxs-lookup"><span data-stu-id="08b45-245">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="08b45-246">Pro událost `oninput` (`@bind-value:event="oninput"`) dojde k reverzi po stisknutí klávesy, která zavádí neanalyzovatelné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="08b45-246">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="08b45-247">Při cílení na událost `oninput` s typem svázaným `int`je uživateli zabráněno v psaní `.`ho znaku.</span><span class="sxs-lookup"><span data-stu-id="08b45-247">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="08b45-248">Znak `.` je okamžitě odebrán, takže uživatel obdrží okamžitou zpětnou vazbu, že jsou povolena pouze celá čísla.</span><span class="sxs-lookup"><span data-stu-id="08b45-248">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="08b45-249">Existují situace, kdy se hodnota `oninput` události nedoporučuje, například pokud by měl být uživatel povolen k vymazání neanalyzovatelné `<input>` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="08b45-249">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="08b45-250">K alternativám patří:</span><span class="sxs-lookup"><span data-stu-id="08b45-250">Alternatives include:</span></span>

* <span data-ttu-id="08b45-251">Nepoužívejte událost `oninput`.</span><span class="sxs-lookup"><span data-stu-id="08b45-251">Don't use the `oninput` event.</span></span> <span data-ttu-id="08b45-252">Použijte výchozí `onchange` událost (`@bind="{PROPERTY OR FIELD}"`), kde neplatnou hodnotu vrátí, dokud prvek neztratí fokus.</span><span class="sxs-lookup"><span data-stu-id="08b45-252">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="08b45-253">Vytvořte propojení s typem s možnou hodnotou null, například `int?` nebo `string`, a poskytněte vlastní logiku pro zpracování neplatných položek.</span><span class="sxs-lookup"><span data-stu-id="08b45-253">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="08b45-254">Použijte [komponentu ověřování formuláře](xref:blazor/forms-validation), například `InputNumber` nebo `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="08b45-254">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="08b45-255">Komponenty ověřování formuláře mají integrovanou podporu pro správu neplatných vstupů.</span><span class="sxs-lookup"><span data-stu-id="08b45-255">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="08b45-256">Součásti pro ověření formuláře:</span><span class="sxs-lookup"><span data-stu-id="08b45-256">Form validation components:</span></span>
  * <span data-ttu-id="08b45-257">Povolí uživateli zadání neplatných vstupů a přijetí chyb ověřování na přidruženém `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="08b45-257">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="08b45-258">Zobrazí chyby ověřování v uživatelském rozhraní, aniž by došlo ke konfliktu s uživatelem, který zadává další data z formuláře.</span><span class="sxs-lookup"><span data-stu-id="08b45-258">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

### <a name="globalization"></a><span data-ttu-id="08b45-259">Globalizace</span><span class="sxs-lookup"><span data-stu-id="08b45-259">Globalization</span></span>

<span data-ttu-id="08b45-260">hodnoty `@bind` jsou formátovány pro zobrazení a analýzu pomocí pravidel aktuální jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="08b45-260">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="08b45-261">K aktuální jazykové verzi je možné přistupovat z vlastnosti <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="08b45-261">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="08b45-262">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) se používá pro následující typy polí (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="08b45-262">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="08b45-263">Předchozí typy polí:</span><span class="sxs-lookup"><span data-stu-id="08b45-263">The preceding field types:</span></span>

* <span data-ttu-id="08b45-264">Se zobrazují pomocí příslušných pravidel formátování založených na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="08b45-264">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="08b45-265">Text s volným formulářem nemůže obsahovat.</span><span class="sxs-lookup"><span data-stu-id="08b45-265">Can't contain free-form text.</span></span>
* <span data-ttu-id="08b45-266">Poskytněte charakteristiky interakce s uživatelem na základě implementace prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="08b45-266">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="08b45-267">Následující typy polí mají specifické požadavky na formátování a aktuálně nejsou podporovány v Blazor, protože nejsou podporovány ve všech hlavních prohlížečích:</span><span class="sxs-lookup"><span data-stu-id="08b45-267">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="08b45-268">`@bind` podporuje parametr `@bind:culture` k poskytnutí <xref:System.Globalization.CultureInfo?displayProperty=fullName> pro analýzu a formátování hodnoty.</span><span class="sxs-lookup"><span data-stu-id="08b45-268">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="08b45-269">Při použití typů polí `date` a `number` se nedoporučuje zadat jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="08b45-269">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="08b45-270">`date` a `number` mají integrovanou podporu Blazor, která poskytuje požadovanou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="08b45-270">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="08b45-271">Informace o tom, jak nastavit jazykovou verzi uživatele, najdete v části [lokalizace](#localization) .</span><span class="sxs-lookup"><span data-stu-id="08b45-271">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

### <a name="format-strings"></a><span data-ttu-id="08b45-272">Řetězce formátu</span><span class="sxs-lookup"><span data-stu-id="08b45-272">Format strings</span></span>

<span data-ttu-id="08b45-273">Datové vazby fungují s <xref:System.DateTime> formátovacími řetězci pomocí [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="08b45-273">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="08b45-274">Jiné formátovací výrazy, jako je například Měna nebo formáty čísel, nejsou v tuto chvíli k dispozici.</span><span class="sxs-lookup"><span data-stu-id="08b45-274">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="08b45-275">V předchozím kódu je výchozí hodnota typu pole `<input>` elementu (`type`) `text`.</span><span class="sxs-lookup"><span data-stu-id="08b45-275">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="08b45-276">`@bind:format` se podporuje pro vázání následujících typů .NET:</span><span class="sxs-lookup"><span data-stu-id="08b45-276">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="08b45-277"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="08b45-277"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="08b45-278"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="08b45-278"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="08b45-279">Atribut `@bind:format` určuje formát data, který má být použit pro `value` elementu `<input>`.</span><span class="sxs-lookup"><span data-stu-id="08b45-279">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="08b45-280">Formát se používá také k analýze hodnoty při výskytu události `onchange`.</span><span class="sxs-lookup"><span data-stu-id="08b45-280">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="08b45-281">Zadání formátu pro typ pole `date` se nedoporučuje, protože Blazor má integrovanou podporu pro formátování dat.</span><span class="sxs-lookup"><span data-stu-id="08b45-281">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="08b45-282">Navzdory doporučení použijte formát data `yyyy-MM-dd`, aby vazba fungovala správně, pokud je zadán typ `date` pole:</span><span class="sxs-lookup"><span data-stu-id="08b45-282">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

### <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="08b45-283">Vazba nadřazený-to-podřízený s parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="08b45-283">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="08b45-284">Vazba rozpoznává parametry komponenty, kde `@bind-{property}` mohou svázat hodnotu vlastnosti z nadřazené komponenty dolů až k podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="08b45-284">Binding recognizes component parameters, where `@bind-{property}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="08b45-285">Vazba z podřízeného na nadřazenou položku je popsaná v tématu [vazba typu podřízená k nadřazenému s zřetězenou](#child-to-parent-binding-with-chained-bind) vazbou.</span><span class="sxs-lookup"><span data-stu-id="08b45-285">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="08b45-286">Následující podřízená komponenta (`ChildComponent`) má parametr `Year` součásti a `YearChanged` zpětného volání:</span><span class="sxs-lookup"><span data-stu-id="08b45-286">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="08b45-287">`EventCallback<T>` je vysvětleno v části [vnořenou eventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="08b45-287">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="08b45-288">Následující nadřazená komponenta používá:</span><span class="sxs-lookup"><span data-stu-id="08b45-288">The following parent component uses:</span></span>

* <span data-ttu-id="08b45-289">`ChildComponent` a váže parametr `ParentYear` z nadřazené položky k parametru `Year` pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-289">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="08b45-290">Událost `onclick` slouží ke spuštění metody `ChangeTheYear`.</span><span class="sxs-lookup"><span data-stu-id="08b45-290">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="08b45-291">Další informace najdete v části [zpracování událostí](#event-handling) .</span><span class="sxs-lookup"><span data-stu-id="08b45-291">For more information, see the [Event handling](#event-handling) section.</span></span>

```razor
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

<span data-ttu-id="08b45-292">Načtení `ParentComponent` vytvoří následující kód:</span><span class="sxs-lookup"><span data-stu-id="08b45-292">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="08b45-293">Pokud je hodnota vlastnosti `ParentYear` změněna výběrem tlačítka v `ParentComponent`, je aktualizována vlastnost `Year` `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="08b45-293">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="08b45-294">Nová hodnota `Year` se vykreslí v uživatelském rozhraní, když se `ParentComponent` znovu vykreslí:</span><span class="sxs-lookup"><span data-stu-id="08b45-294">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="08b45-295">Parametr `Year` je svázán, protože má doprovodnou událost `YearChanged`, která odpovídá typu `Year` parametru.</span><span class="sxs-lookup"><span data-stu-id="08b45-295">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="08b45-296">Podle konvence `<ChildComponent @bind-Year="ParentYear" />` v podstatě ekvivalentem zápisu:</span><span class="sxs-lookup"><span data-stu-id="08b45-296">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="08b45-297">Obecně platí, že vlastnost může být svázána s odpovídající obslužnou rutinou události pomocí atributu `@bind-property:event`.</span><span class="sxs-lookup"><span data-stu-id="08b45-297">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="08b45-298">Například vlastnost `MyProp` může být svázána s `MyEventHandler` pomocí následujících dvou atributů:</span><span class="sxs-lookup"><span data-stu-id="08b45-298">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

### <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="08b45-299">Vazba podřízenosti k nadřazené položce s zřetězenou vazbou</span><span class="sxs-lookup"><span data-stu-id="08b45-299">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="08b45-300">Běžným scénářem je zřetězení parametru vázaného na data na element stránky ve výstupu součásti.</span><span class="sxs-lookup"><span data-stu-id="08b45-300">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="08b45-301">Tento scénář se nazývá *zřetězená vazba* , protože k více úrovním vazby dochází současně.</span><span class="sxs-lookup"><span data-stu-id="08b45-301">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="08b45-302">Zřetězenou BIND nelze implementovat s syntaxí `@bind` v elementu stránky.</span><span class="sxs-lookup"><span data-stu-id="08b45-302">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="08b45-303">Obslužná rutina události a hodnota se musí zadat samostatně.</span><span class="sxs-lookup"><span data-stu-id="08b45-303">The event handler and value must be specified separately.</span></span> <span data-ttu-id="08b45-304">Nadřazená komponenta však může použít `@bind` syntaxe s parametrem komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-304">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="08b45-305">Následující součást `PasswordField` (*PasswordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="08b45-305">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="08b45-306">Nastaví hodnotu `<input>` elementu na vlastnost `Password`.</span><span class="sxs-lookup"><span data-stu-id="08b45-306">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="08b45-307">Zpřístupňuje změny vlastnosti `Password` nadřazené komponentě pomocí [vnořenou eventCallback](#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="08b45-307">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>
* <span data-ttu-id="08b45-308">Používá událost `onclick` slouží ke spuštění metody `ToggleShowPassword`.</span><span class="sxs-lookup"><span data-stu-id="08b45-308">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="08b45-309">Další informace najdete v části [zpracování událostí](#event-handling) .</span><span class="sxs-lookup"><span data-stu-id="08b45-309">For more information, see the [Event handling](#event-handling) section.</span></span>

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="08b45-310">Komponenta `PasswordField` se používá v jiné součásti:</span><span class="sxs-lookup"><span data-stu-id="08b45-310">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="08b45-311">Chcete-li provést kontrolu nebo chyby depeše v předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="08b45-311">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="08b45-312">Vytvořte pole zálohování pro `Password` (`_password` v následujícím ukázkovém kódu).</span><span class="sxs-lookup"><span data-stu-id="08b45-312">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="08b45-313">Proveďte kontroly nebo chyby depeší ve `Password` setter.</span><span class="sxs-lookup"><span data-stu-id="08b45-313">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="08b45-314">Následující příklad poskytuje okamžitou zpětnou vazbu uživateli, pokud se v hodnotě hesla používá mezera:</span><span class="sxs-lookup"><span data-stu-id="08b45-314">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

### <a name="radio-buttons"></a><span data-ttu-id="08b45-315">Přepínací tlačítka</span><span class="sxs-lookup"><span data-stu-id="08b45-315">Radio buttons</span></span>

<span data-ttu-id="08b45-316">Informace o vazbě na přepínače ve formuláři naleznete v tématu <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="08b45-316">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>

## <a name="event-handling"></a><span data-ttu-id="08b45-317">Zpracování událostí</span><span class="sxs-lookup"><span data-stu-id="08b45-317">Event handling</span></span>

<span data-ttu-id="08b45-318">Komponenty Razor poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="08b45-318">Razor components provide event handling features.</span></span> <span data-ttu-id="08b45-319">Pro atribut elementu HTML s názvem `on{EVENT}` (například `onclick` a `onsubmit`) s hodnotou delegovanou typem, komponenty Razor považují hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="08b45-319">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="08b45-320">Název atributu je vždy formátován [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="08b45-320">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="08b45-321">Následující kód volá metodu `UpdateHeading`, pokud je tlačítko vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="08b45-321">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
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

<span data-ttu-id="08b45-322">Následující kód volá metodu `CheckChanged`, když je zaškrtávací políčko v uživatelském rozhraní změněno:</span><span class="sxs-lookup"><span data-stu-id="08b45-322">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="08b45-323">Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="08b45-323">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="08b45-324">Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="08b45-324">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="08b45-325">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="08b45-325">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="08b45-326">V následujícím příkladu je `UpdateHeading` volána asynchronně, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="08b45-326">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
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

### <a name="event-argument-types"></a><span data-ttu-id="08b45-327">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="08b45-327">Event argument types</span></span>

<span data-ttu-id="08b45-328">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="08b45-328">For some events, event argument types are permitted.</span></span> <span data-ttu-id="08b45-329">Pokud přístup k některému z těchto typů událostí není nezbytný, není nutné ve volání metody.</span><span class="sxs-lookup"><span data-stu-id="08b45-329">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="08b45-330">Podporované `EventArgs` jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="08b45-330">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="08b45-331">Událost</span><span class="sxs-lookup"><span data-stu-id="08b45-331">Event</span></span>            | <span data-ttu-id="08b45-332">Třída</span><span class="sxs-lookup"><span data-stu-id="08b45-332">Class</span></span>                | <span data-ttu-id="08b45-333">Události a poznámky modelu DOM</span><span class="sxs-lookup"><span data-stu-id="08b45-333">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="08b45-334">Schránka</span><span class="sxs-lookup"><span data-stu-id="08b45-334">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="08b45-335">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="08b45-335">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="08b45-336">Přetažení</span><span class="sxs-lookup"><span data-stu-id="08b45-336">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="08b45-337">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="08b45-337">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="08b45-338">`DataTransfer` a `DataTransferItem` uchování přetažených dat položky.</span><span class="sxs-lookup"><span data-stu-id="08b45-338">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="08b45-339">Chyba</span><span class="sxs-lookup"><span data-stu-id="08b45-339">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="08b45-340">Událost</span><span class="sxs-lookup"><span data-stu-id="08b45-340">Event</span></span>            | `EventArgs`          | <span data-ttu-id="08b45-341">*Obecné*</span><span class="sxs-lookup"><span data-stu-id="08b45-341">*General*</span></span><br><span data-ttu-id="08b45-342">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="08b45-342">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="08b45-343">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="08b45-343">*Clipboard*</span></span><br><span data-ttu-id="08b45-344">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="08b45-344">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="08b45-345">*Input* (Vstup)</span><span class="sxs-lookup"><span data-stu-id="08b45-345">*Input*</span></span><br><span data-ttu-id="08b45-346">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart``onsubmit`</span><span class="sxs-lookup"><span data-stu-id="08b45-346">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="08b45-347">*Média*</span><span class="sxs-lookup"><span data-stu-id="08b45-347">*Media*</span></span><br><span data-ttu-id="08b45-348">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="08b45-348">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="08b45-349">Vybrána</span><span class="sxs-lookup"><span data-stu-id="08b45-349">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="08b45-350">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="08b45-350">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="08b45-351">Neobsahuje podporu pro `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="08b45-351">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="08b45-352">Vstup</span><span class="sxs-lookup"><span data-stu-id="08b45-352">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="08b45-353">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="08b45-353">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="08b45-354">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="08b45-354">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="08b45-355">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="08b45-355">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="08b45-356">Stisknut</span><span class="sxs-lookup"><span data-stu-id="08b45-356">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="08b45-357">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="08b45-357">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="08b45-358">Ukazatele myši</span><span class="sxs-lookup"><span data-stu-id="08b45-358">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="08b45-359">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="08b45-359">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="08b45-360">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="08b45-360">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="08b45-361">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="08b45-361">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="08b45-362">Průběh</span><span class="sxs-lookup"><span data-stu-id="08b45-362">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="08b45-363">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress``ontimeout`</span><span class="sxs-lookup"><span data-stu-id="08b45-363">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="08b45-364">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="08b45-364">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="08b45-365">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave``ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="08b45-365">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="08b45-366">`TouchPoint` představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="08b45-366">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="08b45-367">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="08b45-367">For more information, see the following resources:</span></span>

* <span data-ttu-id="08b45-368">[Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="08b45-368">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="08b45-369">[MDN web Docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="08b45-369">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="08b45-370">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="08b45-370">Lambda expressions</span></span>

<span data-ttu-id="08b45-371">Lambda výrazy lze také použít:</span><span class="sxs-lookup"><span data-stu-id="08b45-371">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="08b45-372">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="08b45-372">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="08b45-373">Následující příklad vytvoří tři tlačítka, z nichž každá volá `UpdateHeading` předání argumentu události (`MouseEventArgs`) a jeho číslo tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="08b45-373">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="08b45-374">Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="08b45-374">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="08b45-375">V opačném případě se stejná proměnná používá u všech výrazů lambda, které způsobují, že hodnota `i`být stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="08b45-375">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="08b45-376">Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="08b45-376">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="08b45-377">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="08b45-377">EventCallback</span></span>

<span data-ttu-id="08b45-378">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti&mdash;například při výskytu události `onclick` v podřízeném objektu.</span><span class="sxs-lookup"><span data-stu-id="08b45-378">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="08b45-379">Chcete-li zobrazit události napříč komponentami, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="08b45-379">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="08b45-380">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené `EventCallback`komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-380">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="08b45-381">`ChildComponent` v ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak je nastavená obslužná rutina `onclick` tlačítka pro příjem `EventCallback`ho delegáta z `ParentComponent`ukázky.</span><span class="sxs-lookup"><span data-stu-id="08b45-381">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="08b45-382">`EventCallback` se zadává pomocí `MouseEventArgs`, která je vhodná pro událost `onclick` z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="08b45-382">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="08b45-383">`ParentComponent` nastaví `EventCallback<T>` (`OnClickCallback`) dítěte na svou `ShowMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="08b45-383">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="08b45-384">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-384">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="08b45-385">Když je vybráno tlačítko v `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="08b45-385">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="08b45-386">Je volána metoda `ShowMessage` `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="08b45-386">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="08b45-387">`_messageText` se aktualizuje a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="08b45-387">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="08b45-388">Volání [StateHasChanged](xref:blazor/lifecycle#state-changes) není vyžadováno v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="08b45-388">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="08b45-389">`StateHasChanged` se zavolá automaticky, aby se `ParentComponent`znovu vykreslila, stejně jako podřízené události spouštějí revykreslování komponenty v obslužných rutinách události, které se spouštějí v rámci podřízeného objektu</span><span class="sxs-lookup"><span data-stu-id="08b45-389">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="08b45-390">`EventCallback` a `EventCallback<T>` povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="08b45-390">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="08b45-391">`EventCallback<T>` je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-391">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="08b45-392">`EventCallback` je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-392">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="08b45-393">Vyvolat `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a očekávat <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="08b45-393">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="08b45-394">Pro zpracování událostí a parametry komponenty vazby použijte `EventCallback` a `EventCallback<T>`.</span><span class="sxs-lookup"><span data-stu-id="08b45-394">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="08b45-395">Preferovat `EventCallback<T>` silného typu přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="08b45-395">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="08b45-396">`EventCallback<T>` poskytuje uživatelům součásti lepší zpětnou vazbu k chybám.</span><span class="sxs-lookup"><span data-stu-id="08b45-396">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="08b45-397">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="08b45-397">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="08b45-398">Použijte `EventCallback`, pokud není předána žádná hodnota zpětnému volání.</span><span class="sxs-lookup"><span data-stu-id="08b45-398">Use `EventCallback` when there's no value passed to the callback.</span></span>

### <a name="prevent-default-actions"></a><span data-ttu-id="08b45-399">Zabránit výchozím akcím</span><span class="sxs-lookup"><span data-stu-id="08b45-399">Prevent default actions</span></span>

<span data-ttu-id="08b45-400">Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) .</span><span class="sxs-lookup"><span data-stu-id="08b45-400">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="08b45-401">Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="08b45-401">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="08b45-402">V následujícím příkladu je znemožněno výchozí chování zadáním atributu direktiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="08b45-402">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="08b45-403">Čítač zvýší a **+** klíč není zachycen do hodnoty `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="08b45-403">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="08b45-404">Zadání atributu `@on{EVENT}:preventDefault` bez hodnoty je ekvivalentem `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="08b45-404">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="08b45-405">Hodnotou atributu může být také výraz.</span><span class="sxs-lookup"><span data-stu-id="08b45-405">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="08b45-406">V následujícím příkladu je `_shouldPreventDefault` `bool` pole nastaveno na hodnotu `true` nebo `false`:</span><span class="sxs-lookup"><span data-stu-id="08b45-406">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="08b45-407">Obslužná rutina události není nutná, aby se zabránilo výchozí akci.</span><span class="sxs-lookup"><span data-stu-id="08b45-407">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="08b45-408">Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.</span><span class="sxs-lookup"><span data-stu-id="08b45-408">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="08b45-409">Zastavit šíření událostí</span><span class="sxs-lookup"><span data-stu-id="08b45-409">Stop event propagation</span></span>

<span data-ttu-id="08b45-410">Pro zastavení šíření události použijte atribut direktiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) .</span><span class="sxs-lookup"><span data-stu-id="08b45-410">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="08b45-411">V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` z rozšiřování do nadřazené `<div>`:</span><span class="sxs-lookup"><span data-stu-id="08b45-411">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="08b45-412">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="08b45-412">Capture references to components</span></span>

<span data-ttu-id="08b45-413">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="08b45-413">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="08b45-414">Zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="08b45-414">To capture a component reference:</span></span>

* <span data-ttu-id="08b45-415">Přidejte atribut [`@ref`](xref:mvc/views/razor#ref) pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-415">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="08b45-416">Definujte pole stejného typu jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="08b45-416">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="08b45-417">Při vykreslení komponenty je pole `_loginDialog` vyplněno instancí `MyLoginDialog` podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="08b45-417">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="08b45-418">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-418">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="08b45-419">Proměnná `_loginDialog` je naplněna pouze po vykreslení komponenty a její výstup obsahuje prvek `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="08b45-419">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="08b45-420">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="08b45-420">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="08b45-421">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="08b45-421">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="08b45-422">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/javascript-interop#capture-references-to-elements), není to funkce [interoperability JavaScriptu](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="08b45-422">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="08b45-423">Odkazy na součásti nejsou předány kódu jazyka JavaScript&mdash;jsou používány pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="08b45-423">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="08b45-424">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="08b45-424">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="08b45-425">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="08b45-425">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="08b45-426">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="08b45-426">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="08b45-427">Vyvolat metody komponenty externě na stav aktualizace</span><span class="sxs-lookup"><span data-stu-id="08b45-427">Invoke component methods externally to update state</span></span>

<span data-ttu-id="08b45-428">Blazor používá `SynchronizationContext` k vykonání jednoho logického vlákna provádění.</span><span class="sxs-lookup"><span data-stu-id="08b45-428">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="08b45-429">V tomto `SynchronizationContext`jsou spouštěny [metody životního cyklu](xref:blazor/lifecycle) komponenty a všechna zpětná volání událostí, která jsou aktivována nástrojem Blazor.</span><span class="sxs-lookup"><span data-stu-id="08b45-429">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="08b45-430">V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte metodu `InvokeAsync`, která se odešle do `SynchronizationContext`Blazor.</span><span class="sxs-lookup"><span data-stu-id="08b45-430">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="08b45-431">Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:</span><span class="sxs-lookup"><span data-stu-id="08b45-431">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="08b45-432">Zaregistrujte `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="08b45-432">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="08b45-433">V Blazor WebAssembly Zaregistrujte službu v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="08b45-433">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="08b45-434">V Blazor serveru Zaregistrujte službu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="08b45-434">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="08b45-435">K aktualizaci součásti použijte `NotifierService`:</span><span class="sxs-lookup"><span data-stu-id="08b45-435">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="08b45-436">V předchozím příkladu `NotifierService` vyvolá metodu `OnNotify` komponenty mimo Blazor `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="08b45-436">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="08b45-437">`InvokeAsync` slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.</span><span class="sxs-lookup"><span data-stu-id="08b45-437">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="08b45-438">Použití \@Key k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="08b45-438">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="08b45-439">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazor rozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="08b45-439">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="08b45-440">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="08b45-440">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="08b45-441">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="08b45-441">Consider the following example:</span></span>

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

<span data-ttu-id="08b45-442">Obsah kolekce `People` se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="08b45-442">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="08b45-443">Při revykreslování komponenty se může komponenta `<DetailsEditor>` změnit, aby přijímala jiné hodnoty parametrů `Details`.</span><span class="sxs-lookup"><span data-stu-id="08b45-443">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="08b45-444">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="08b45-444">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="08b45-445">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="08b45-445">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="08b45-446">Proces mapování lze řídit pomocí atributu direktivy `@key`.</span><span class="sxs-lookup"><span data-stu-id="08b45-446">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="08b45-447">`@key` způsobí, že rozdílový algoritmus zaručuje uchovávání prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="08b45-447">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="08b45-448">Když dojde ke změně kolekce `People`, rozdílový algoritmus zachovává přidružení mezi instancemi `<DetailsEditor>` a instancemi `person`:</span><span class="sxs-lookup"><span data-stu-id="08b45-448">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="08b45-449">Pokud se ze seznamu `People` odstraní `Person`, z uživatelského rozhraní se odebere jenom odpovídající instance `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="08b45-449">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="08b45-450">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="08b45-450">Other instances are left unchanged.</span></span>
* <span data-ttu-id="08b45-451">Pokud je na některém místě v seznamu vložená `Person`, do příslušné pozice se vloží jedna nová instance `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="08b45-451">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="08b45-452">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="08b45-452">Other instances are left unchanged.</span></span>
* <span data-ttu-id="08b45-453">Pokud jsou položky `Person` znovu seřazeny, odpovídající instance `<DetailsEditor>` jsou zachovány a v uživatelském rozhraní znovu seřazeny.</span><span class="sxs-lookup"><span data-stu-id="08b45-453">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="08b45-454">V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhnete se potenciálním problémům se měnícími se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="08b45-454">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="08b45-455">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-455">Keys are local to each container element or component.</span></span> <span data-ttu-id="08b45-456">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="08b45-456">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="08b45-457">Kdy použít klíč \@</span><span class="sxs-lookup"><span data-stu-id="08b45-457">When to use \@key</span></span>

<span data-ttu-id="08b45-458">Obvykle má smysl použít `@key` vždy, když je vygenerován seznam (například v bloku `@foreach`), a existuje vhodná hodnota pro definování `@key`.</span><span class="sxs-lookup"><span data-stu-id="08b45-458">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="08b45-459">Můžete také použít `@key`, chcete-li zabránit Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="08b45-459">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="08b45-460">Pokud `@currentPerson` změny, direktiva `@key` atributu vynutí Blazor zahodit celé `<div>` a jeho následníky a znovu sestavit podstrom v uživatelském rozhraní s novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="08b45-460">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="08b45-461">To může být užitečné, pokud potřebujete zaručit, že při `@currentPerson` změny se nezachová žádný stav uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="08b45-461">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="08b45-462">Kdy se \@klíč nepoužívá</span><span class="sxs-lookup"><span data-stu-id="08b45-462">When not to use \@key</span></span>

<span data-ttu-id="08b45-463">Při rozdílech s `@key`se účtují náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="08b45-463">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="08b45-464">Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="08b45-464">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="08b45-465">I když `@key` nepoužívá, Blazor zachová podřízený element a instance komponenty co nejvíc.</span><span class="sxs-lookup"><span data-stu-id="08b45-465">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="08b45-466">Jedinou výhodou použití `@key` je řídit, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="08b45-466">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="08b45-467">Jaké hodnoty se mají použít pro klíč \@</span><span class="sxs-lookup"><span data-stu-id="08b45-467">What values to use for \@key</span></span>

<span data-ttu-id="08b45-468">Obecně je vhodné dodat jeden z následujících typů hodnoty pro `@key`:</span><span class="sxs-lookup"><span data-stu-id="08b45-468">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="08b45-469">Instance objektů modelu (například instance `Person` jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="08b45-469">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="08b45-470">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="08b45-470">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="08b45-471">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="08b45-471">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="08b45-472">Zajistěte, aby hodnoty používané pro `@key` nekolidovat.</span><span class="sxs-lookup"><span data-stu-id="08b45-472">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="08b45-473">Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty střetu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo komponenty na nové prvky nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-473">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="08b45-474">Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="08b45-474">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="08b45-475">Směrování</span><span class="sxs-lookup"><span data-stu-id="08b45-475">Routing</span></span>

<span data-ttu-id="08b45-476">Směrování v Blazor se dosahuje tak, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="08b45-476">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="08b45-477">Když je zkompilován soubor Razor s direktivou `@page`, vygenerovaná třída má <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="08b45-477">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="08b45-478">V době běhu směrovač vyhledá třídy komponent pomocí `RouteAttribute` a vykreslí, že každá komponenta má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="08b45-478">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="08b45-479">Pro komponentu lze použít více šablon směrování.</span><span class="sxs-lookup"><span data-stu-id="08b45-479">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="08b45-480">Následující komponenta reaguje na požadavky na `/BlazorRoute` a `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="08b45-480">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="08b45-481">*Stránky/BlazorRoute. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-481">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="08b45-482">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="08b45-482">Route parameters</span></span>

<span data-ttu-id="08b45-483">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v direktivě `@page`.</span><span class="sxs-lookup"><span data-stu-id="08b45-483">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="08b45-484">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="08b45-484">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="08b45-485">*Stránky/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-485">*Pages/RouteParameter.razor*:</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="08b45-486">Volitelné parametry nejsou podporované, takže se v předchozím příkladu použijí dvě direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="08b45-486">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="08b45-487">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="08b45-487">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="08b45-488">Druhá direktiva `@page` přebírá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.</span><span class="sxs-lookup"><span data-stu-id="08b45-488">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="08b45-489">*Catch-All –* syntaxe parametru (`*`/`**`), která zachycuje cestu mezi více hranicemi složek, **není v** součástech Razor ( *. Razor*) podporována.</span><span class="sxs-lookup"><span data-stu-id="08b45-489">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="08b45-490">Podpora částečné třídy</span><span class="sxs-lookup"><span data-stu-id="08b45-490">Partial class support</span></span>

<span data-ttu-id="08b45-491">Komponenty Razor jsou generovány jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="08b45-491">Razor components are generated as partial classes.</span></span> <span data-ttu-id="08b45-492">Komponenty Razor jsou vytvořeny některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="08b45-492">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="08b45-493">C#kód je definován v [`@code`](xref:mvc/views/razor#code) bloku pomocí značek HTML a kódu Razor v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="08b45-493">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="08b45-494">Šablony Blazor definují své komponenty Razor pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="08b45-494">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="08b45-495">C#kód je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.</span><span class="sxs-lookup"><span data-stu-id="08b45-495">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="08b45-496">Následující příklad ukazuje výchozí komponentu `Counter` s blokem `@code` v aplikaci vygenerovanou šablonou Blazor.</span><span class="sxs-lookup"><span data-stu-id="08b45-496">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="08b45-497">Značky HTML, kód Razor a C# kód jsou ve stejném souboru:</span><span class="sxs-lookup"><span data-stu-id="08b45-497">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="08b45-498">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-498">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="08b45-499">Komponentu `Counter` lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:</span><span class="sxs-lookup"><span data-stu-id="08b45-499">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="08b45-500">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-500">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="08b45-501">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="08b45-501">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="08b45-502">Podle potřeby přidejte všechny požadované obory názvů do souboru dílčí třídy.</span><span class="sxs-lookup"><span data-stu-id="08b45-502">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="08b45-503">Mezi obvyklé obory názvů používané komponentami Razor patří:</span><span class="sxs-lookup"><span data-stu-id="08b45-503">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="08b45-504">Zadat základní třídu</span><span class="sxs-lookup"><span data-stu-id="08b45-504">Specify a base class</span></span>

<span data-ttu-id="08b45-505">Direktivu [`@inherits`](xref:mvc/views/razor#inherits) lze použít k určení základní třídy pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="08b45-505">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="08b45-506">Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`, k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-506">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="08b45-507">Základní třída by měla být odvozena od `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="08b45-507">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="08b45-508">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="08b45-508">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="08b45-509">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="08b45-509">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="08b45-510">Zadat atribut</span><span class="sxs-lookup"><span data-stu-id="08b45-510">Specify an attribute</span></span>

<span data-ttu-id="08b45-511">Atributy lze zadat v součástech Razor s direktivou [`@attribute`](xref:mvc/views/razor#attribute) .</span><span class="sxs-lookup"><span data-stu-id="08b45-511">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="08b45-512">Následující příklad používá atribut `[Authorize]` pro třídu komponenty:</span><span class="sxs-lookup"><span data-stu-id="08b45-512">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="08b45-513">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="08b45-513">Import components</span></span>

<span data-ttu-id="08b45-514">Obor názvů komponenty, která je vytvořená pomocí Razor, je založen na (v pořadí podle priority):</span><span class="sxs-lookup"><span data-stu-id="08b45-514">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="08b45-515">[`@namespace`](xref:mvc/views/razor#namespace) označení v souboru Razor ( *. Razor*) značky (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="08b45-515">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="08b45-516">`RootNamespace` projektu v souboru projektu (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="08b45-516">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="08b45-517">Název projektu, pořízený z názvu souboru projektu ( *. csproj*) a cesta z kořenového adresáře projektu ke komponentě.</span><span class="sxs-lookup"><span data-stu-id="08b45-517">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="08b45-518">Například rozhraní řeší *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="08b45-518">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="08b45-519">Komponenty následují C# pravidla vazeb názvů.</span><span class="sxs-lookup"><span data-stu-id="08b45-519">Components follow C# name binding rules.</span></span> <span data-ttu-id="08b45-520">Pro komponentu `Index` v tomto příkladu komponenty v oboru jsou všechny komponenty:</span><span class="sxs-lookup"><span data-stu-id="08b45-520">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="08b45-521">Ve stejné složce *stránky*.</span><span class="sxs-lookup"><span data-stu-id="08b45-521">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="08b45-522">Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.</span><span class="sxs-lookup"><span data-stu-id="08b45-522">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="08b45-523">Komponenty definované v jiném oboru názvů se přenesou do rozsahu pomocí direktivy [`@using`](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="08b45-523">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="08b45-524">Pokud v *BlazorSample/Shared/* Folder existuje jiná komponenta, `NavMenu.razor`, lze komponentu použít v `Index.razor` pomocí následujícího příkazu `@using`:</span><span class="sxs-lookup"><span data-stu-id="08b45-524">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="08b45-525">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují direktivu [`@using`](xref:mvc/views/razor#using) :</span><span class="sxs-lookup"><span data-stu-id="08b45-525">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="08b45-526">Kvalifikace `global::` není podporována.</span><span class="sxs-lookup"><span data-stu-id="08b45-526">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="08b45-527">Import komponent s aliasy `using` příkazy (například `@using Foo = Bar`) není podporován.</span><span class="sxs-lookup"><span data-stu-id="08b45-527">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="08b45-528">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="08b45-528">Partially qualified names aren't supported.</span></span> <span data-ttu-id="08b45-529">Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="08b45-529">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="08b45-530">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="08b45-530">Conditional HTML element attributes</span></span>

<span data-ttu-id="08b45-531">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="08b45-531">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="08b45-532">Pokud je hodnota `false` nebo `null`, není atribut vykreslen.</span><span class="sxs-lookup"><span data-stu-id="08b45-532">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="08b45-533">Pokud je hodnota `true`, vykreslí se atribut jako minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="08b45-533">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="08b45-534">V následujícím příkladu `IsCompleted` určuje, zda je `checked` vykreslen v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="08b45-534">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="08b45-535">Pokud je `IsCompleted` `true`, zaškrtávací políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="08b45-535">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="08b45-536">Pokud je `IsCompleted` `false`, zaškrtávací políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="08b45-536">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="08b45-537">Další informace najdete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="08b45-537">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="08b45-538">Některé atributy HTML, jako je například [Standard ARIA](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nebudou fungovat správně, pokud je typ .NET `bool`.</span><span class="sxs-lookup"><span data-stu-id="08b45-538">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="08b45-539">V těchto případech použijte místo `bool``string` typ.</span><span class="sxs-lookup"><span data-stu-id="08b45-539">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="08b45-540">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="08b45-540">Raw HTML</span></span>

<span data-ttu-id="08b45-541">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="08b45-541">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="08b45-542">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="08b45-542">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="08b45-543">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="08b45-543">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="08b45-544">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="08b45-544">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="08b45-545">Následující příklad ukazuje použití typu `MarkupString` pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="08b45-545">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="08b45-546">Komponenty se šablonami</span><span class="sxs-lookup"><span data-stu-id="08b45-546">Templated components</span></span>

<span data-ttu-id="08b45-547">Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-547">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="08b45-548">Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="08b45-548">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="08b45-549">Mezi několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="08b45-549">A couple of examples include:</span></span>

* <span data-ttu-id="08b45-550">Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="08b45-550">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="08b45-551">Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="08b45-551">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="08b45-552">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="08b45-552">Template parameters</span></span>

<span data-ttu-id="08b45-553">Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="08b45-553">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="08b45-554">Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="08b45-554">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="08b45-555">`RenderFragment<T>` převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.</span><span class="sxs-lookup"><span data-stu-id="08b45-555">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="08b45-556">součást `TableTemplate`:</span><span class="sxs-lookup"><span data-stu-id="08b45-556">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="08b45-557">Při použití komponenty se šablonou lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="08b45-557">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
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

### <a name="template-context-parameters"></a><span data-ttu-id="08b45-558">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="08b45-558">Template context parameters</span></span>

<span data-ttu-id="08b45-559">Argumenty součásti typu `RenderFragment<T>` předány jako elementy mají implicitní parametr s názvem `context` (například z předchozí ukázky kódu `@context.PetId`), ale můžete změnit název parametru pomocí atributu `Context` u podřízeného elementu.</span><span class="sxs-lookup"><span data-stu-id="08b45-559">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="08b45-560">V následujícím příkladu atribut `Context` elementu `RowTemplate` určuje parametr `pet`:</span><span class="sxs-lookup"><span data-stu-id="08b45-560">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
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

<span data-ttu-id="08b45-561">Alternativně lze zadat atribut `Context` prvku komponenty.</span><span class="sxs-lookup"><span data-stu-id="08b45-561">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="08b45-562">Zadaný atribut `Context` se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="08b45-562">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="08b45-563">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu).</span><span class="sxs-lookup"><span data-stu-id="08b45-563">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="08b45-564">V následujícím příkladu se atribut `Context` zobrazí u elementu `TableTemplate` a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="08b45-564">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
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

### <a name="generic-typed-components"></a><span data-ttu-id="08b45-565">Komponenty s obecným typem</span><span class="sxs-lookup"><span data-stu-id="08b45-565">Generic-typed components</span></span>

<span data-ttu-id="08b45-566">Komponenty se šablonami jsou často typu obecně typované.</span><span class="sxs-lookup"><span data-stu-id="08b45-566">Templated components are often generically typed.</span></span> <span data-ttu-id="08b45-567">Například obecná `ListViewTemplate` komponenta může být použita pro vykreslení `IEnumerable<T>`ch hodnot.</span><span class="sxs-lookup"><span data-stu-id="08b45-567">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="08b45-568">Chcete-li definovat obecné komponenty, použijte direktivu [`@typeparam`](xref:mvc/views/razor#typeparam) pro určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="08b45-568">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="08b45-569">Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="08b45-569">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="08b45-570">V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="08b45-570">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="08b45-571">V následujícím příkladu `TItem="Pet"` určuje typ:</span><span class="sxs-lookup"><span data-stu-id="08b45-571">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="08b45-572">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="08b45-572">Cascading values and parameters</span></span>

<span data-ttu-id="08b45-573">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="08b45-573">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="08b45-574">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="08b45-574">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="08b45-575">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="08b45-575">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="08b45-576">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="08b45-576">Theme example</span></span>

<span data-ttu-id="08b45-577">V následujícím příkladu z ukázkové aplikace určuje třída `ThemeInfo` informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="08b45-577">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="08b45-578">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="08b45-578">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="08b45-579">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="08b45-579">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="08b45-580">Komponenta `CascadingValue` zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="08b45-580">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="08b45-581">Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří text rozložení `@Body` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="08b45-581">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="08b45-582">`ButtonClass` je v komponentě rozložení přiřazena hodnota `btn-success`.</span><span class="sxs-lookup"><span data-stu-id="08b45-582">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="08b45-583">Jakákoli následná komponenta může tuto vlastnost spotřebovat prostřednictvím `ThemeInfo` kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="08b45-583">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="08b45-584">součást `CascadingValuesParametersLayout`:</span><span class="sxs-lookup"><span data-stu-id="08b45-584">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="08b45-585">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí atributu `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="08b45-585">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="08b45-586">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="08b45-586">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="08b45-587">V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta váže kaskádovou hodnotu `ThemeInfo` na kaskádový parametr.</span><span class="sxs-lookup"><span data-stu-id="08b45-587">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="08b45-588">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="08b45-588">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="08b45-589">součást `CascadingValuesParametersTheme`:</span><span class="sxs-lookup"><span data-stu-id="08b45-589">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

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
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="08b45-590">Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` komponentu a odpovídající `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="08b45-590">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="08b45-591">V následujícím příkladu se dvě `CascadingValue` komponenty zanášejí do různých instancí `MyCascadingType` podle názvu:</span><span class="sxs-lookup"><span data-stu-id="08b45-591">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="08b45-592">V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="08b45-592">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="08b45-593">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="08b45-593">TabSet example</span></span>

<span data-ttu-id="08b45-594">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="08b45-594">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="08b45-595">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="08b45-595">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="08b45-596">Ukázková aplikace má `ITab` rozhraní, které tyto karty implementují:</span><span class="sxs-lookup"><span data-stu-id="08b45-596">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="08b45-597">Komponenta `CascadingValuesParametersTabSet` používá součást `TabSet`, která obsahuje několik `Tab` komponent:</span><span class="sxs-lookup"><span data-stu-id="08b45-597">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="08b45-598">Podřízené `Tab` komponenty nejsou explicitně předány jako parametry `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="08b45-598">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="08b45-599">Místo toho jsou podřízené součásti `Tab` součástí podřízeného obsahu `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="08b45-599">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="08b45-600">`TabSet` však stále potřebuje znát každou součást `Tab`, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, komponenta `TabSet` *může poskytnout sebe sama jako kaskádovou hodnotu* , která je poté odebrána pomocí podřízených `Tab` komponent.</span><span class="sxs-lookup"><span data-stu-id="08b45-600">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="08b45-601">součást `TabSet`:</span><span class="sxs-lookup"><span data-stu-id="08b45-601">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="08b45-602">Podřízené `Tab` komponenty zachytí obsahující `TabSet` jako kaskádový parametr, takže součásti `Tab` přidají sebe do `TabSet` a koordinují, na které kartě je aktivní.</span><span class="sxs-lookup"><span data-stu-id="08b45-602">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="08b45-603">součást `Tab`:</span><span class="sxs-lookup"><span data-stu-id="08b45-603">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="08b45-604">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="08b45-604">Razor templates</span></span>

<span data-ttu-id="08b45-605">Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor.</span><span class="sxs-lookup"><span data-stu-id="08b45-605">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="08b45-606">Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="08b45-606">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="08b45-607">Následující příklad ukazuje, jak určit hodnoty `RenderFragment` a `RenderFragment<T>` a vykreslit šablony přímo v komponentě.</span><span class="sxs-lookup"><span data-stu-id="08b45-607">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="08b45-608">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="08b45-608">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="08b45-609">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="08b45-609">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="08b45-610">Ruční logika RenderTreeBuilder</span><span class="sxs-lookup"><span data-stu-id="08b45-610">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="08b45-611">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` poskytuje metody pro práci s komponentami a prvky, včetně sestavování C# komponent ručně v kódu.</span><span class="sxs-lookup"><span data-stu-id="08b45-611">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="08b45-612">Použití `RenderTreeBuilder` k vytváření komponent je pokročilý scénář.</span><span class="sxs-lookup"><span data-stu-id="08b45-612">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="08b45-613">Poškozená komponenta (například značka neuzavřeného označení) může mít za následek nedefinované chování.</span><span class="sxs-lookup"><span data-stu-id="08b45-613">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="08b45-614">Vezměte v úvahu následující součást `PetDetails`, kterou je možné ručně vytvořit do jiné komponenty:</span><span class="sxs-lookup"><span data-stu-id="08b45-614">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="08b45-615">V následujícím příkladu smyčka v metodě `CreateComponent` generuje tři komponenty `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="08b45-615">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="08b45-616">Při volání metod `RenderTreeBuilder` pro vytvoření komponent (`OpenComponent` a `AddAttribute`) jsou pořadová čísla čísla řádků zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="08b45-616">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="08b45-617">Algoritmus rozdílu Blazor spoléhá na pořadová čísla, která odpovídají jedinečným řádkům kódu, a neliší vyvolání volání.</span><span class="sxs-lookup"><span data-stu-id="08b45-617">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="08b45-618">Při vytváření komponenty s metodami `RenderTreeBuilder` nekódujte pevně argumenty pro pořadová čísla.</span><span class="sxs-lookup"><span data-stu-id="08b45-618">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="08b45-619">**Použití výpočtu nebo čítače k vygenerování pořadového čísla může vést k špatnému výkonu.**</span><span class="sxs-lookup"><span data-stu-id="08b45-619">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="08b45-620">Další informace naleznete v části [pořadové číslo se vztahuje na čísla řádků kódu a nikoli na oddíl pořadí provádění](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="08b45-620">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="08b45-621">součást `BuiltContent`:</span><span class="sxs-lookup"><span data-stu-id="08b45-621">`BuiltContent` component:</span></span>

```razor
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

> [!WARNING]
> <span data-ttu-id="08b45-622">Typy v `Microsoft.AspNetCore.Components.RenderTree` umožňují zpracování *výsledků* operací vykreslování.</span><span class="sxs-lookup"><span data-stu-id="08b45-622">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="08b45-623">Jedná se o interní podrobnosti implementace Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="08b45-623">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="08b45-624">Tyto typy by měly být považovány za *nestabilní* a mohou se změnit v budoucích verzích.</span><span class="sxs-lookup"><span data-stu-id="08b45-624">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="08b45-625">Pořadová čísla se vztahují na čísla řádků kódu a nikoli na pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="08b45-625">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="08b45-626">Soubory Blazor `.razor` jsou vždy kompilovány.</span><span class="sxs-lookup"><span data-stu-id="08b45-626">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="08b45-627">Tato možnost je potenciálně výhodná pro `.razor`, protože krok kompilace lze použít k vložení informací, které zlepšují výkon aplikace za běhu.</span><span class="sxs-lookup"><span data-stu-id="08b45-627">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="08b45-628">Hlavní příklad těchto vylepšení zahrnuje *pořadová čísla*.</span><span class="sxs-lookup"><span data-stu-id="08b45-628">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="08b45-629">Pořadová čísla označují modul runtime, ze kterého výstupy pocházejí, ze kterých se liší a seřazené řádky kódu.</span><span class="sxs-lookup"><span data-stu-id="08b45-629">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="08b45-630">Modul runtime používá tyto informace k vygenerování efektivních rozdílů stromu v lineárním čase, což je mnohem rychlejší než obvykle pro obecný rozdílový algoritmus stromu.</span><span class="sxs-lookup"><span data-stu-id="08b45-630">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="08b45-631">Vezměte v úvahu následující soubor součásti Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="08b45-631">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="08b45-632">Předchozí kód se zkompiluje jako následující:</span><span class="sxs-lookup"><span data-stu-id="08b45-632">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="08b45-633">Při prvním spuštění kódu, pokud je `someFlag` `true`, tvůrce obdrží:</span><span class="sxs-lookup"><span data-stu-id="08b45-633">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="08b45-634">Pořadí</span><span class="sxs-lookup"><span data-stu-id="08b45-634">Sequence</span></span> | <span data-ttu-id="08b45-635">Typ</span><span class="sxs-lookup"><span data-stu-id="08b45-635">Type</span></span>      | <span data-ttu-id="08b45-636">Data</span><span class="sxs-lookup"><span data-stu-id="08b45-636">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="08b45-637">0</span><span class="sxs-lookup"><span data-stu-id="08b45-637">0</span></span>        | <span data-ttu-id="08b45-638">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="08b45-638">Text node</span></span> | <span data-ttu-id="08b45-639">První</span><span class="sxs-lookup"><span data-stu-id="08b45-639">First</span></span>  |
| <span data-ttu-id="08b45-640">1</span><span class="sxs-lookup"><span data-stu-id="08b45-640">1</span></span>        | <span data-ttu-id="08b45-641">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="08b45-641">Text node</span></span> | <span data-ttu-id="08b45-642">Sekunda</span><span class="sxs-lookup"><span data-stu-id="08b45-642">Second</span></span> |

<span data-ttu-id="08b45-643">Představte si, že `someFlag` se budou `false`a kód se znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="08b45-643">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="08b45-644">Tentokrát Tvůrce získá:</span><span class="sxs-lookup"><span data-stu-id="08b45-644">This time, the builder receives:</span></span>

| <span data-ttu-id="08b45-645">Pořadí</span><span class="sxs-lookup"><span data-stu-id="08b45-645">Sequence</span></span> | <span data-ttu-id="08b45-646">Typ</span><span class="sxs-lookup"><span data-stu-id="08b45-646">Type</span></span>       | <span data-ttu-id="08b45-647">Data</span><span class="sxs-lookup"><span data-stu-id="08b45-647">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="08b45-648">1</span><span class="sxs-lookup"><span data-stu-id="08b45-648">1</span></span>        | <span data-ttu-id="08b45-649">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="08b45-649">Text node</span></span>  | <span data-ttu-id="08b45-650">Sekunda</span><span class="sxs-lookup"><span data-stu-id="08b45-650">Second</span></span> |

<span data-ttu-id="08b45-651">Pokud modul runtime provede rozdíl, uvidí, že položka v sekvenci `0` byla odebrána, takže generuje následující skript triviálního *úprav*:</span><span class="sxs-lookup"><span data-stu-id="08b45-651">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="08b45-652">Odeberte první textový uzel.</span><span class="sxs-lookup"><span data-stu-id="08b45-652">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="08b45-653">Co je špatné, pokud generujete pořadová čísla programově</span><span class="sxs-lookup"><span data-stu-id="08b45-653">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="08b45-654">Představte si, že jste napsali následující logiku tvůrce stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="08b45-654">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="08b45-655">Teď je první výstup:</span><span class="sxs-lookup"><span data-stu-id="08b45-655">Now, the first output is:</span></span>

| <span data-ttu-id="08b45-656">Pořadí</span><span class="sxs-lookup"><span data-stu-id="08b45-656">Sequence</span></span> | <span data-ttu-id="08b45-657">Typ</span><span class="sxs-lookup"><span data-stu-id="08b45-657">Type</span></span>      | <span data-ttu-id="08b45-658">Data</span><span class="sxs-lookup"><span data-stu-id="08b45-658">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="08b45-659">0</span><span class="sxs-lookup"><span data-stu-id="08b45-659">0</span></span>        | <span data-ttu-id="08b45-660">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="08b45-660">Text node</span></span> | <span data-ttu-id="08b45-661">První</span><span class="sxs-lookup"><span data-stu-id="08b45-661">First</span></span>  |
| <span data-ttu-id="08b45-662">1</span><span class="sxs-lookup"><span data-stu-id="08b45-662">1</span></span>        | <span data-ttu-id="08b45-663">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="08b45-663">Text node</span></span> | <span data-ttu-id="08b45-664">Sekunda</span><span class="sxs-lookup"><span data-stu-id="08b45-664">Second</span></span> |

<span data-ttu-id="08b45-665">Tento výsledek je stejný jako předchozí případ, takže neexistují žádné negativní problémy.</span><span class="sxs-lookup"><span data-stu-id="08b45-665">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="08b45-666">`someFlag` je `false` při druhém vykreslování a výstup je:</span><span class="sxs-lookup"><span data-stu-id="08b45-666">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="08b45-667">Pořadí</span><span class="sxs-lookup"><span data-stu-id="08b45-667">Sequence</span></span> | <span data-ttu-id="08b45-668">Typ</span><span class="sxs-lookup"><span data-stu-id="08b45-668">Type</span></span>      | <span data-ttu-id="08b45-669">Data</span><span class="sxs-lookup"><span data-stu-id="08b45-669">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="08b45-670">0</span><span class="sxs-lookup"><span data-stu-id="08b45-670">0</span></span>        | <span data-ttu-id="08b45-671">Textový uzel</span><span class="sxs-lookup"><span data-stu-id="08b45-671">Text node</span></span> | <span data-ttu-id="08b45-672">Sekunda</span><span class="sxs-lookup"><span data-stu-id="08b45-672">Second</span></span> |

<span data-ttu-id="08b45-673">Tentokrát rozdílový algoritmus uvidí, že došlo ke *dvěma* změnám, a algoritmus generuje následující skript pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="08b45-673">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="08b45-674">Změňte hodnotu prvního textového uzlu na `Second`.</span><span class="sxs-lookup"><span data-stu-id="08b45-674">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="08b45-675">Odeberte druhý textový uzel.</span><span class="sxs-lookup"><span data-stu-id="08b45-675">Remove the second text node.</span></span>

<span data-ttu-id="08b45-676">Generování pořadových čísel ztratilo všechny užitečné informace o tom, kde `if/else` větve a smyčky byly obsaženy v původním kódu.</span><span class="sxs-lookup"><span data-stu-id="08b45-676">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="08b45-677">To vede ke rozdílu **dvakrát, jak dlouho** chcete.</span><span class="sxs-lookup"><span data-stu-id="08b45-677">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="08b45-678">Toto je triviální příklad.</span><span class="sxs-lookup"><span data-stu-id="08b45-678">This is a trivial example.</span></span> <span data-ttu-id="08b45-679">Ve složitějších případech se složitými a hluboce vnořenými strukturami, a to hlavně pomocí smyček, náklady na výkon jsou závažnější.</span><span class="sxs-lookup"><span data-stu-id="08b45-679">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="08b45-680">Místo toho, aby se ihned identifikovaly, které bloky nebo větve smyček byly vloženy nebo odebrány, je třeba rozdílový algoritmus přepracovat hluboko do stromů vykreslování a obvykle sestavovat mnohem delší úpravu skriptů, protože je nepřehledně neinformován o tom, jak staré a nové struktury vzájemná vazba.</span><span class="sxs-lookup"><span data-stu-id="08b45-680">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="08b45-681">Doprovodné materiály a závěry</span><span class="sxs-lookup"><span data-stu-id="08b45-681">Guidance and conclusions</span></span>

* <span data-ttu-id="08b45-682">Pokud jsou automaticky generována pořadová čísla, je výkon aplikace zhoršen.</span><span class="sxs-lookup"><span data-stu-id="08b45-682">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="08b45-683">Rozhraní nemůže automaticky vytvořit vlastní pořadová čísla za běhu, protože potřebné informace neexistují, pokud není zachycena v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="08b45-683">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="08b45-684">Nepište dlouhé bloky manuálně implementované `RenderTreeBuilder` logiky.</span><span class="sxs-lookup"><span data-stu-id="08b45-684">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="08b45-685">Preferovat `.razor` soubory a umožněte kompilátoru, aby se zabývat pořadovým číslem.</span><span class="sxs-lookup"><span data-stu-id="08b45-685">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="08b45-686">Nemůžete-li se vyhnout ruční logice `RenderTreeBuilder`, rozdělte dlouhé bloky kódu do menších částí zabalených do `OpenRegion`/`CloseRegion` volání.</span><span class="sxs-lookup"><span data-stu-id="08b45-686">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="08b45-687">Každá oblast má vlastní oddělený prostor pořadových čísel, takže v každé oblasti můžete restartovat z nuly (nebo jakéhokoli jiného libovolného čísla).</span><span class="sxs-lookup"><span data-stu-id="08b45-687">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="08b45-688">Pokud jsou pořadová čísla pevně zakódované, rozdílový algoritmus vyžaduje pouze zvýšení hodnoty pořadových čísel.</span><span class="sxs-lookup"><span data-stu-id="08b45-688">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="08b45-689">Počáteční hodnota a mezery jsou nepodstatné.</span><span class="sxs-lookup"><span data-stu-id="08b45-689">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="08b45-690">Jednou z oprávněných možností je použít číslo řádku kódu jako pořadové číslo nebo začít od nuly a zvýšit podle hodnoty nebo stovky (případně z upřednostňovaného intervalu).</span><span class="sxs-lookup"><span data-stu-id="08b45-690">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="08b45-691"> používá pořadová čísla, zatímco jiné architektury uživatelského rozhraní rozdílového stromu je nepoužívají.</span><span class="sxs-lookup"><span data-stu-id="08b45-691"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="08b45-692">Rozdíly jsou mnohem rychlejší, když se používají pořadová čísla, a Blazor má výhodu kompilačního kroku, který se automaticky zabývá pořadovým číslem pro vývojáře vytvářející soubory *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="08b45-692">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="08b45-693">Lokalizace</span><span class="sxs-lookup"><span data-stu-id="08b45-693">Localization</span></span>

<span data-ttu-id="08b45-694">aplikace Blazor serveru jsou lokalizovány pomocí [middlewaru pro lokalizaci](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="08b45-694">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="08b45-695">Middleware vybere vhodnou jazykovou verzi pro uživatele, kteří žádají o prostředky z aplikace.</span><span class="sxs-lookup"><span data-stu-id="08b45-695">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="08b45-696">Tuto jazykovou verzi lze nastavit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="08b45-696">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="08b45-697">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="08b45-697">Cookies</span></span>](#cookies)
* [<span data-ttu-id="08b45-698">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="08b45-698">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="08b45-699">Další informace a příklady naleznete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="08b45-699">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="08b45-700">Konfigurace linkeru pro mezinárodní navýšení (Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="08b45-700">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="08b45-701">Ve výchozím nastavení Blazorkonfigurace linkeru pro Blazor aplikace pro WebAssembly odříznout informace o mezinárodním prostředí s výjimkou výslovně požadovaných místních hodnot.</span><span class="sxs-lookup"><span data-stu-id="08b45-701">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="08b45-702">Další informace a pokyny k řízení chování linkeru naleznete v tématu <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="08b45-702">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="08b45-703">Soubory cookie</span><span class="sxs-lookup"><span data-stu-id="08b45-703">Cookies</span></span>

<span data-ttu-id="08b45-704">Soubor cookie lokalizační kultury může zachovat jazykovou verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="08b45-704">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="08b45-705">Soubor cookie je vytvořen metodou `OnGet` stránky hostitele aplikace (*Pages/Host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="08b45-705">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="08b45-706">Middleware lokalizace přečte soubor cookie při následných požadavcích na nastavení jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="08b45-706">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="08b45-707">Použití souboru cookie zajistí, že připojení protokolu WebSocket dokáže správně rozšířit jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="08b45-707">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="08b45-708">Pokud jsou schémata lokalizací založena na cestě URL nebo řetězci dotazu, nemusí být schopná pracovat s objekty WebSockets, takže nepůjde zachovat jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="08b45-708">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="08b45-709">Proto je doporučený přístup použití souboru cookie lokalizační kultury.</span><span class="sxs-lookup"><span data-stu-id="08b45-709">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="08b45-710">Pokud je jazyková verze uložena v souboru cookie lokalizace, je možné použít jakoukoli techniku k přiřazení jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="08b45-710">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="08b45-711">Pokud už aplikace má zavedené lokalizační schéma pro ASP.NET Core na straně serveru, pokračujte v používání stávající infrastruktury lokalizace a nastavte soubor cookie lokalizační kultury v rámci schématu aplikace.</span><span class="sxs-lookup"><span data-stu-id="08b45-711">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="08b45-712">Následující příklad ukazuje, jak nastavit aktuální jazykovou verzi v souboru cookie, který lze přečíst pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="08b45-712">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="08b45-713">Vytvořte soubor *Pages/Host. cshtml. cs* s následujícím obsahem v aplikaci Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="08b45-713">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="08b45-714">Lokalizace se zpracovává v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="08b45-714">Localization is handled in the app:</span></span>

1. <span data-ttu-id="08b45-715">Prohlížeč pošle do aplikace počáteční požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="08b45-715">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="08b45-716">Jazyková verze je přiřazena pomocí middleware Localization.</span><span class="sxs-lookup"><span data-stu-id="08b45-716">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="08b45-717">Metoda `OnGet` v *_Host. cshtml. cs* uchovává v rámci odpovědi jazykovou verzi v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="08b45-717">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="08b45-718">Prohlížeč otevře připojení pomocí protokolu WebSocket a vytvoří interaktivní relaci serveru Blazor.</span><span class="sxs-lookup"><span data-stu-id="08b45-718">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="08b45-719">Middleware lokalizace přečte soubor cookie a přiřadí jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="08b45-719">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="08b45-720">Relace Blazor serveru začíná správnou jazykovou verzí.</span><span class="sxs-lookup"><span data-stu-id="08b45-720">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="08b45-721">Poskytnutí uživatelského rozhraní pro výběr jazykové verze</span><span class="sxs-lookup"><span data-stu-id="08b45-721">Provide UI to choose the culture</span></span>

<span data-ttu-id="08b45-722">K poskytnutí uživatelského rozhraní, které uživateli umožní vybrat jazykovou verzi, se doporučuje *přístup založený na přesměrování* .</span><span class="sxs-lookup"><span data-stu-id="08b45-722">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="08b45-723">Proces se podobá tomu, co se stane ve webové aplikaci, když se uživatel pokusí o přístup k zabezpečenému prostředku&mdash;uživatel je přesměrován na přihlašovací stránku a pak se přesměruje zpátky na původní prostředek.</span><span class="sxs-lookup"><span data-stu-id="08b45-723">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="08b45-724">Aplikace zachovává vybranou jazykovou verzi uživatele prostřednictvím přesměrování na kontroler.</span><span class="sxs-lookup"><span data-stu-id="08b45-724">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="08b45-725">Řadič Nastaví vybranou jazykovou verzi uživatele na soubor cookie a přesměruje uživatele zpět na původní identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="08b45-725">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="08b45-726">Vytvořte koncový bod HTTP na serveru pro nastavení vybrané jazykové verze uživatele v souboru cookie a proveďte přesměrování zpátky na původní identifikátor URI:</span><span class="sxs-lookup"><span data-stu-id="08b45-726">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="08b45-727">Použijte `LocalRedirect` výsledek akce, aby se zabránilo otevřeným útokům přes přesměrování.</span><span class="sxs-lookup"><span data-stu-id="08b45-727">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="08b45-728">Další informace najdete v tématu <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="08b45-728">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="08b45-729">Následující komponenta ukazuje příklad, jak provést počáteční přesměrování, když uživatel vybere jazykovou verzi:</span><span class="sxs-lookup"><span data-stu-id="08b45-729">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="08b45-730">Použití scénářů lokalizace rozhraní .NET v aplikacích Blazor</span><span class="sxs-lookup"><span data-stu-id="08b45-730">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="08b45-731">V rámci Blazor aplikací jsou k dispozici následující scénáře lokalizace a globalizace rozhraní .NET:</span><span class="sxs-lookup"><span data-stu-id="08b45-731">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="08b45-732">. Systém prostředků netto</span><span class="sxs-lookup"><span data-stu-id="08b45-732">.NET's resources system</span></span>
* <span data-ttu-id="08b45-733">Formátování čísla a data specifické pro jazykovou verzi</span><span class="sxs-lookup"><span data-stu-id="08b45-733">Culture-specific number and date formatting</span></span>

<span data-ttu-id="08b45-734">funkce `@bind` Blazorprovádí globalizaci na základě aktuální jazykové verze uživatele.</span><span class="sxs-lookup"><span data-stu-id="08b45-734">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="08b45-735">Další informace najdete v části [vázání dat](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="08b45-735">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="08b45-736">V současné době se podporuje omezená sada scénářů lokalizace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="08b45-736">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="08b45-737">`IStringLocalizer<>` *se* v aplikacích Blazor podporuje.</span><span class="sxs-lookup"><span data-stu-id="08b45-737">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="08b45-738">lokalizace `IHtmlLocalizer<>`, `IViewLocalizer<>`a datových poznámek jsou ASP.NET Core scénáře MVC a **nejsou podporovány** v aplikacích Blazor.</span><span class="sxs-lookup"><span data-stu-id="08b45-738">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="08b45-739">Další informace najdete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="08b45-739">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="08b45-740">Obrázky ve formátu SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="08b45-740">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="08b45-741">Vzhledem k tomu, že Blazor vykresluje HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) ( *. SVG*), jsou podporovány prostřednictvím značky `<img>`:</span><span class="sxs-lookup"><span data-stu-id="08b45-741">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="08b45-742">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů ( *. CSS*):</span><span class="sxs-lookup"><span data-stu-id="08b45-742">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="08b45-743">Vložené značky SVG se však ve všech scénářích nepodporují.</span><span class="sxs-lookup"><span data-stu-id="08b45-743">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="08b45-744">Pokud značku `<svg>` umístíte přímo do souboru komponenty ( *. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů se ještě nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="08b45-744">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="08b45-745">Například `<use>` značky nejsou aktuálně dodrženy a `@bind` nelze použít s některými značkami SVG.</span><span class="sxs-lookup"><span data-stu-id="08b45-745">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="08b45-746">Očekáváme, že tato omezení vyřešíme v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="08b45-746">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08b45-747">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="08b45-747">Additional resources</span></span>

* <span data-ttu-id="08b45-748"><xref:security/blazor/server> &ndash; obsahuje pokyny k vytváření Blazor serverových aplikací, které se musí soupeří s vyčerpáním prostředků.</span><span class="sxs-lookup"><span data-stu-id="08b45-748"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
