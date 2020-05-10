---
title: Vytvoření a použití komponent Razor ASP.NET Core
author: guardrex
description: Naučte se vytvářet a používat Razor komponenty, včetně toho, jak navazovat data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: de8696488292dfc409c5ed83ea0b88a54a001696
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003062"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="a7bff-103">Vytvoření a použití komponent Razor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7bff-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="a7bff-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="a7bff-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="a7bff-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7bff-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="a7bff-106">aplikace jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="a7bff-106"> apps are built using *components*.</span></span> <span data-ttu-id="a7bff-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="a7bff-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="a7bff-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7bff-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="a7bff-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="a7bff-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="a7bff-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="a7bff-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="a7bff-111">Component classes</span></span>

<span data-ttu-id="a7bff-112">Komponenty jsou implementovány [Razor](xref:mvc/views/razor) v souborech součástí (*. Razor*) pomocí kombinace kódu jazyka C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="a7bff-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="a7bff-113">Komponenta v Blazor je formálně označována jako \* Razor komponenta\*.</span><span class="sxs-lookup"><span data-stu-id="a7bff-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="a7bff-114">Název součásti musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="a7bff-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="a7bff-115">Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.</span><span class="sxs-lookup"><span data-stu-id="a7bff-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="a7bff-116">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="a7bff-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="a7bff-117">Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="a7bff-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="a7bff-118">Při kompilaci aplikace jsou značky HTML a vykreslování jazyka C# převedeny na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="a7bff-119">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="a7bff-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="a7bff-120">Členy třídy komponenty jsou definovány v `@code` bloku.</span><span class="sxs-lookup"><span data-stu-id="a7bff-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="a7bff-121">V `@code` bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="a7bff-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="a7bff-122">Je přípustný více `@code` než jeden blok.</span><span class="sxs-lookup"><span data-stu-id="a7bff-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="a7bff-123">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí výrazů jazyka C#, které začínají na `@`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="a7bff-124">Například pole C# se vykreslí pomocí předpony `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="a7bff-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="a7bff-125">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="a7bff-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="a7bff-126">`headingFontStyle`do hodnoty vlastnosti CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-126">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="a7bff-127">`headingText`k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-127">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="a7bff-128">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="a7bff-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="a7bff-129">pak porovná nový strom vykreslování s předchozí a aplikuje všechny úpravy model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="a7bff-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="a7bff-130">Komponenty jsou běžné třídy jazyka C# a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="a7bff-131">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="a7bff-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="a7bff-132">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="a7bff-133">Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7bff-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="a7bff-134">Pokud je `BlazorApp` kořenový obor názvů aplikace a `Counter` součást se nachází ve složce *stránky* :</span><span class="sxs-lookup"><span data-stu-id="a7bff-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="a7bff-135">Obor `Counter` názvů součásti je `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="a7bff-136">Plně kvalifikovaný název typu komponenty je `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="a7bff-137">Další informace naleznete v části [Import komponent](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="a7bff-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="a7bff-138">Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky buď do nadřazené komponenty, nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7bff-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="a7bff-139">Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je `BlazorApp`kořenový obor názvů aplikace:</span><span class="sxs-lookup"><span data-stu-id="a7bff-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="a7bff-140">Statické prostředky</span><span class="sxs-lookup"><span data-stu-id="a7bff-140">Static assets</span></span>

Blazor<span data-ttu-id="a7bff-141">postupuje podle konvence ASP.NET Core aplikací, které umísťují statické prostředky do [složky webového kořenového adresáře (wwwroot)](xref:fundamentals/index#web-root)projektu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="a7bff-142">Použijte základní cestu (`/`) pro odkaz na webový kořenový adresář pro statický prostředek.</span><span class="sxs-lookup"><span data-stu-id="a7bff-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="a7bff-143">V následujícím příkladu je soubor *logo. png* fyzicky umístěný ve složce *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="a7bff-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="a7bff-144">komponenty **nepodporují** vlnové lomítko (`~/`).</span><span class="sxs-lookup"><span data-stu-id="a7bff-144"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="a7bff-145">Informace o nastavení základní cesty aplikace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="a7bff-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="a7bff-146">V součástech nejsou podporovány pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="a7bff-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="a7bff-147">V Razor součástech (soubory *. Razor* ) nejsou podporované [pomocníky značek](xref:mvc/views/tag-helpers/intro) .</span><span class="sxs-lookup"><span data-stu-id="a7bff-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="a7bff-148">Chcete-li poskytnout funkci Blazor, jako je například Pomocník pro vytváření značek, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="a7bff-149">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="a7bff-149">Use components</span></span>

<span data-ttu-id="a7bff-150">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="a7bff-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="a7bff-151">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="a7bff-152">Následující kód v *indexu. Razor* vykreslí `HeadingComponent` instanci:</span><span class="sxs-lookup"><span data-stu-id="a7bff-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="a7bff-153">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="a7bff-154">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="a7bff-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="a7bff-155">Přidání `@using` direktivy pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.</span><span class="sxs-lookup"><span data-stu-id="a7bff-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="a7bff-156">Směrování</span><span class="sxs-lookup"><span data-stu-id="a7bff-156">Routing</span></span>

<span data-ttu-id="a7bff-157">Směrování do Blazor se dosahuje tím, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="a7bff-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="a7bff-158">Když je Razor zkompilován soubor s `@page` direktivou, vygenerovaná třída je dána <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadáním šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="a7bff-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="a7bff-159">V době běhu Směrovač vyhledává třídy komponent pomocí `RouteAttribute` a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="a7bff-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="a7bff-160">Další informace naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="a7bff-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="a7bff-161">Parametry</span><span class="sxs-lookup"><span data-stu-id="a7bff-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="a7bff-162">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="a7bff-162">Route parameters</span></span>

<span data-ttu-id="a7bff-163">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v `@page` direktivě.</span><span class="sxs-lookup"><span data-stu-id="a7bff-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="a7bff-164">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="a7bff-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="a7bff-165">*Stránky/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="a7bff-166">Volitelné parametry nejsou podporovány, takže v `@page` předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="a7bff-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="a7bff-167">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="a7bff-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="a7bff-168">Druhá `@page` direktiva přijme parametr `{text}` Route a přiřadí hodnotu `Text` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a7bff-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="a7bff-169">*Catch-All* Parameter Syntax (`*`/`**`), která zachycuje cestu mezi více hranicemi složek **, není v** Razor součástech (*. Razor*) podporován.</span><span class="sxs-lookup"><span data-stu-id="a7bff-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="a7bff-170">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="a7bff-170">Component parameters</span></span>

<span data-ttu-id="a7bff-171">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s `[Parameter]` atributem.</span><span class="sxs-lookup"><span data-stu-id="a7bff-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="a7bff-172">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="a7bff-173">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="a7bff-174">V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu `Title` vlastnosti. `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="a7bff-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="a7bff-175">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="a7bff-176">Nevytvářejte komponenty, které zapisují do vlastních *parametrů komponenty*, místo toho použijte soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="a7bff-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="a7bff-177">Další informace naleznete v části [Nevytvářet komponenty, které jsou zapsány do jejich vlastních vlastností parametrů](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="a7bff-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="a7bff-178">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="a7bff-178">Child content</span></span>

<span data-ttu-id="a7bff-179">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="a7bff-179">Components can set the content of another component.</span></span> <span data-ttu-id="a7bff-180">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="a7bff-181">V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje `RenderFragment`, který představuje segment uživatelského rozhraní pro vykreslení.</span><span class="sxs-lookup"><span data-stu-id="a7bff-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="a7bff-182">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="a7bff-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="a7bff-183">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v panelu Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="a7bff-184">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="a7bff-185">Vlastnost, která přijímá `RenderFragment` obsah, musí být `ChildContent` pojmenována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="a7bff-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="a7bff-186">`ParentComponent` V ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř `<ChildComponent>` značek.</span><span class="sxs-lookup"><span data-stu-id="a7bff-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="a7bff-187">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="a7bff-188">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="a7bff-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="a7bff-189">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="a7bff-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="a7bff-190">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí [`@attributes`](xref:mvc/views/razor#attributes) Razor direktivy.</span><span class="sxs-lookup"><span data-stu-id="a7bff-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="a7bff-191">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="a7bff-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="a7bff-192">Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="a7bff-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="a7bff-193">V následujícím `<input>` příkladu první prvek (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="a7bff-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="a7bff-194">Typ parametru musí být implementován `IEnumerable<KeyValuePair<string, object>>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="a7bff-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="a7bff-195">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="a7bff-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="a7bff-196">Vykreslené `<input>` elementy pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="a7bff-196">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="a7bff-197">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí `[Parameter]` atributu s `CaptureUnmatchedValues` vlastností nastavenou na `true`:</span><span class="sxs-lookup"><span data-stu-id="a7bff-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="a7bff-198">`CaptureUnmatchedValues` Vlastnost on `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="a7bff-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="a7bff-199">Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="a7bff-200">Typ vlastnosti používaný pomocí `CaptureUnmatchedValues` musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="a7bff-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="a7bff-201">`IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="a7bff-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="a7bff-202">Pozice `@attributes` relativní vzhledem k poloze atributů elementu je důležitá.</span><span class="sxs-lookup"><span data-stu-id="a7bff-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="a7bff-203">Když `@attributes` jsou splatted na elementu, atributy jsou zpracovávány zprava doleva (poslední až první).</span><span class="sxs-lookup"><span data-stu-id="a7bff-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="a7bff-204">Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="a7bff-205">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="a7bff-206">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="a7bff-207">Atribut komponenty je nastaven na hodnotu napravo od `@attributes` `Child` `extra`</span><span class="sxs-lookup"><span data-stu-id="a7bff-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="a7bff-208">Vykreslená `Parent` `<div>` komponenta obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):</span><span class="sxs-lookup"><span data-stu-id="a7bff-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="a7bff-209">V následujícím příkladu je `extra` pořadí a `@attributes` obrácené v `Child` komponentě: `<div>`</span><span class="sxs-lookup"><span data-stu-id="a7bff-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="a7bff-210">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="a7bff-211">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="a7bff-212">Vykreslená `<div>` `Parent` komponenta obsahuje `extra="10"` , když je předána pomocí dalšího atributu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="a7bff-213">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="a7bff-213">Capture references to components</span></span>

<span data-ttu-id="a7bff-214">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="a7bff-215">Zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-215">To capture a component reference:</span></span>

* <span data-ttu-id="a7bff-216">Přidejte [`@ref`](xref:mvc/views/razor#ref) atribut do podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="a7bff-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="a7bff-217">Definujte pole stejného typu jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="a7bff-217">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="a7bff-218">Při vykreslení komponenty je `loginDialog` pole vyplněno instancí `MyLoginDialog` podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="a7bff-218">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="a7bff-219">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7bff-220">`loginDialog` Proměnná je naplněna pouze po vykreslení komponenty a její výstup obsahuje `MyLoginDialog` element.</span><span class="sxs-lookup"><span data-stu-id="a7bff-220">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="a7bff-221">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="a7bff-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="a7bff-222">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="a7bff-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="a7bff-223">Chcete-li odkazovat na součásti ve smyčce, viz [zachytit odkazy na více podobných podřízených komponent (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="a7bff-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="a7bff-224">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="a7bff-225">Odkazy na součásti nejsou předány kódu&mdash;jazyka JavaScript, které jsou používány pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="a7bff-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="a7bff-226">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="a7bff-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="a7bff-227">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="a7bff-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="a7bff-228">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="a7bff-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="a7bff-229">Vyvolat metody komponenty externě na stav aktualizace</span><span class="sxs-lookup"><span data-stu-id="a7bff-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="a7bff-230">používá `SynchronizationContext` k vykonání jediného logického vlákna provádění.</span><span class="sxs-lookup"><span data-stu-id="a7bff-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="a7bff-231">[Metody životního cyklu](xref:blazor/lifecycle) komponenty a všechna zpětná volání událostí, která jsou Blazor aktivována, jsou provedena `SynchronizationContext`na tomto.</span><span class="sxs-lookup"><span data-stu-id="a7bff-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="a7bff-232">V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte `InvokeAsync` metodu, která bude zaslána do. Blazor `SynchronizationContext`</span><span class="sxs-lookup"><span data-stu-id="a7bff-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="a7bff-233">Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="a7bff-234">Zaregistrujte `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="a7bff-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="a7bff-235">V Blazor rámci služby WebAssembly Zaregistrujte `Program.Main`službu v:</span><span class="sxs-lookup"><span data-stu-id="a7bff-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="a7bff-236">V Blazor části Server Zaregistrujte službu `Startup.ConfigureServices`v nástroji:</span><span class="sxs-lookup"><span data-stu-id="a7bff-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="a7bff-237">`NotifierService` K aktualizaci součásti použijte.</span><span class="sxs-lookup"><span data-stu-id="a7bff-237">Use the `NotifierService` to update a component:</span></span>

```razor
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

<span data-ttu-id="a7bff-238">`NotifierService` V předchozím příkladu `OnNotify` vyvolá metodu komponenty mimo Blazor'. `SynchronizationContext`</span><span class="sxs-lookup"><span data-stu-id="a7bff-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="a7bff-239">`InvokeAsync`slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="a7bff-240">Použití \@klíče k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="a7bff-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="a7bff-241">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent se musí rozhodnout, Blazorkterý z předchozích prvků nebo komponent může být zachován a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="a7bff-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="a7bff-242">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="a7bff-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="a7bff-243">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="a7bff-243">Consider the following example:</span></span>

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

<span data-ttu-id="a7bff-244">Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="a7bff-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="a7bff-245">Když se komponenta znovu vykreslí, může `<DetailsEditor>` se tato součást změnit, aby `Details` přijímala jiné hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="a7bff-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="a7bff-246">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="a7bff-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="a7bff-247">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="a7bff-248">Proces mapování lze řídit pomocí atributu [`@key`](xref:mvc/views/razor#key) direktiva.</span><span class="sxs-lookup"><span data-stu-id="a7bff-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="a7bff-249">`@key`způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="a7bff-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="a7bff-250">Když se `People` kolekce změní, rozdílový algoritmus zachovává přidružení mezi `<DetailsEditor>` instancemi a `person` instancemi:</span><span class="sxs-lookup"><span data-stu-id="a7bff-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="a7bff-251">Pokud `Person` se ze `People` seznamu odstraní, z uživatelského rozhraní se odebere `<DetailsEditor>` jenom odpovídající instance.</span><span class="sxs-lookup"><span data-stu-id="a7bff-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="a7bff-252">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="a7bff-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="a7bff-253">Pokud `Person` je vložena na nějaké místo v seznamu, je do příslušné `<DetailsEditor>` pozice vložena jedna nová instance.</span><span class="sxs-lookup"><span data-stu-id="a7bff-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="a7bff-254">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="a7bff-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="a7bff-255">Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7bff-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="a7bff-256">V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a7bff-257">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="a7bff-258">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="a7bff-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="a7bff-259">Kdy použít \@klíč</span><span class="sxs-lookup"><span data-stu-id="a7bff-259">When to use \@key</span></span>

<span data-ttu-id="a7bff-260">Obvykle má smysl použít `@key` při každém vykreslení seznamu (například v `@foreach` bloku) a existuje vhodná hodnota pro definování. `@key`</span><span class="sxs-lookup"><span data-stu-id="a7bff-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="a7bff-261">Můžete také použít `@key` k zabránění Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="a7bff-262">Pokud `@currentPerson` se změní, `@key` direktiva Attribute Blazor vynutí zrušení celého `<div>` a jeho následníků a znovu sestaví podstrom v uživatelském rozhraní novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="a7bff-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="a7bff-263">To může být užitečné, pokud potřebujete zaručit, že se při `@currentPerson` změnách nezachovají stav uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7bff-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="a7bff-264">Kdy nepoužívat \@klíč</span><span class="sxs-lookup"><span data-stu-id="a7bff-264">When not to use \@key</span></span>

<span data-ttu-id="a7bff-265">Při rozdílech s se `@key`účtují náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="a7bff-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="a7bff-266">Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="a7bff-267">I v `@key` případě, že Blazor není použit, zachová podřízený element a instance komponenty co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="a7bff-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="a7bff-268">Jedinou výhodou použití `@key` je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="a7bff-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="a7bff-269">Jaké hodnoty se mají použít \@pro klíč</span><span class="sxs-lookup"><span data-stu-id="a7bff-269">What values to use for \@key</span></span>

<span data-ttu-id="a7bff-270">Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro `@key`:</span><span class="sxs-lookup"><span data-stu-id="a7bff-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="a7bff-271">Instance objektů modelu (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="a7bff-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="a7bff-272">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="a7bff-273">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="a7bff-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="a7bff-274">Zajistěte, aby `@key` hodnoty použité pro nekolidovat.</span><span class="sxs-lookup"><span data-stu-id="a7bff-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="a7bff-275">Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty konfliktu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo součásti na nové prvky nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="a7bff-276">Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="a7bff-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="a7bff-277">Nevytvářejte komponenty, které zapisují do vlastností vlastního parametru.</span><span class="sxs-lookup"><span data-stu-id="a7bff-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="a7bff-278">Parametry jsou přepsány za následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="a7bff-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="a7bff-279">Obsah podřízené komponenty je vykreslen pomocí `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="a7bff-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána v nadřazené komponentě.</span><span class="sxs-lookup"><span data-stu-id="a7bff-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="a7bff-281">Parametry jsou resetovány, protože nadřazená komponenta je znovu <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> vygenerována, když je volána, a podřízené součásti jsou zadány nové hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="a7bff-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="a7bff-282">Vezměte v úvahu `Expander` následující komponentu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="a7bff-283">Vykreslí podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="a7bff-283">Renders child content.</span></span>
* <span data-ttu-id="a7bff-284">Přepíná zobrazení podřízeného obsahu s parametrem součásti.</span><span class="sxs-lookup"><span data-stu-id="a7bff-284">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="a7bff-285">`Expander` Komponenta je přidána do nadřazené komponenty, která může zavolat `StateHasChanged`:</span><span class="sxs-lookup"><span data-stu-id="a7bff-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="a7bff-286">Zpočátku se `Expander` komponenty chovají nezávisle při přepínání `Expanded` jejich vlastností.</span><span class="sxs-lookup"><span data-stu-id="a7bff-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="a7bff-287">Podřízené komponenty udržují své stavy podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="a7bff-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="a7bff-288">Když `StateHasChanged` je volána v nadřazeném prvku, `Expanded` parametr první podřízené komponenty se obnoví zpět na původní hodnotu (`true`).</span><span class="sxs-lookup"><span data-stu-id="a7bff-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="a7bff-289">`Expanded` Hodnota druhé `Expander` součásti není resetována, protože ve druhé komponentě není vykreslen žádný podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="a7bff-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="a7bff-290">Chcete-li zachovat stav v předchozím scénáři, použijte *soukromé pole* v `Expander` součásti k údržbě jeho přepnutého stavu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="a7bff-291">Následující `Expander` součást:</span><span class="sxs-lookup"><span data-stu-id="a7bff-291">The following `Expander` component:</span></span>

* <span data-ttu-id="a7bff-292">Akceptuje `Expanded` hodnotu parametru komponenty z nadřazené položky.</span><span class="sxs-lookup"><span data-stu-id="a7bff-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="a7bff-293">Přiřadí hodnotu parametru komponenty k *privátnímu poli* (`expanded`) v [události s inicializací](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="a7bff-293">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="a7bff-294">Pomocí soukromého pole udržuje stav interního přepínání.</span><span class="sxs-lookup"><span data-stu-id="a7bff-294">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="a7bff-295">Podpora částečné třídy</span><span class="sxs-lookup"><span data-stu-id="a7bff-295">Partial class support</span></span>

Razor<span data-ttu-id="a7bff-296">komponenty jsou generovány jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="a7bff-296"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="a7bff-297">komponenty jsou vytvořeny jedním z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a7bff-297"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="a7bff-298">Kód jazyka C# je definován v [`@code`](xref:mvc/views/razor#code) bloku s označením HTML Razor a kódem v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="a7bff-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="a7bff-299">šablony definují své Razor komponenty pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="a7bff-300">Kód jazyka C# je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.</span><span class="sxs-lookup"><span data-stu-id="a7bff-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="a7bff-301">Následující příklad ukazuje výchozí `Counter` komponentu s `@code` blokem v aplikaci vygenerovanou ze Blazor šablony.</span><span class="sxs-lookup"><span data-stu-id="a7bff-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="a7bff-302">Razor Kód HTML kódu a kód jazyka C# jsou ve stejném souboru:</span><span class="sxs-lookup"><span data-stu-id="a7bff-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="a7bff-303">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-303">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="a7bff-304">`Counter` Komponentu lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:</span><span class="sxs-lookup"><span data-stu-id="a7bff-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="a7bff-305">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="a7bff-306">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-306">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="a7bff-307">Podle potřeby přidejte všechny požadované obory názvů do souboru dílčí třídy.</span><span class="sxs-lookup"><span data-stu-id="a7bff-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="a7bff-308">Mezi obvyklé obory Razor názvů používané komponentami patří:</span><span class="sxs-lookup"><span data-stu-id="a7bff-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="a7bff-309">Zadat základní třídu</span><span class="sxs-lookup"><span data-stu-id="a7bff-309">Specify a base class</span></span>

<span data-ttu-id="a7bff-310">[`@inherits`](xref:mvc/views/razor#inherits) Direktiva se dá použít k určení základní třídy pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="a7bff-311">Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="a7bff-312">Základní třída by měla být odvozena od `ComponentBase`třídy.</span><span class="sxs-lookup"><span data-stu-id="a7bff-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="a7bff-313">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="a7bff-314">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-314">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="a7bff-315">Zadat atribut</span><span class="sxs-lookup"><span data-stu-id="a7bff-315">Specify an attribute</span></span>

<span data-ttu-id="a7bff-316">Atributy lze zadat v Razor součástech s [`@attribute`](xref:mvc/views/razor#attribute) direktivou.</span><span class="sxs-lookup"><span data-stu-id="a7bff-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="a7bff-317">Následující příklad používá `[Authorize]` atribut pro třídu komponenty:</span><span class="sxs-lookup"><span data-stu-id="a7bff-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="a7bff-318">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="a7bff-318">Import components</span></span>

<span data-ttu-id="a7bff-319">Obor názvů komponenty, která se vytvořila Razor , je založený na (v pořadí podle priority):</span><span class="sxs-lookup"><span data-stu-id="a7bff-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="a7bff-320">[`@namespace`](xref:mvc/views/razor#namespace)označení v Razor souboru (*. Razor*) značky (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="a7bff-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="a7bff-321">Projekt `RootNamespace` v souboru projektu (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="a7bff-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="a7bff-322">Název projektu, pořízený z názvu souboru projektu (*. csproj*) a cesta z kořenového adresáře projektu ke komponentě.</span><span class="sxs-lookup"><span data-stu-id="a7bff-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="a7bff-323">Rozhraní například překládá *{root]/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="a7bff-324">Komponenty následují pravidla vazeb názvů C#.</span><span class="sxs-lookup"><span data-stu-id="a7bff-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="a7bff-325">Pro `Index` komponentu v tomto příkladu komponenty v oboru jsou všechny komponenty:</span><span class="sxs-lookup"><span data-stu-id="a7bff-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="a7bff-326">Ve stejné složce *stránky*.</span><span class="sxs-lookup"><span data-stu-id="a7bff-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="a7bff-327">Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.</span><span class="sxs-lookup"><span data-stu-id="a7bff-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="a7bff-328">Komponenty definované v jiném oboru názvů se přenesou do rozsahu Razor [`@using`](xref:mvc/views/razor#using) direktivy using.</span><span class="sxs-lookup"><span data-stu-id="a7bff-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="a7bff-329">Pokud jiná komponenta, `NavMenu.razor`existuje ve složce *BlazorSample/Shared/* Folder, lze komponentu použít v `Index.razor` příkazu s následujícím `@using` příkazem:</span><span class="sxs-lookup"><span data-stu-id="a7bff-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="a7bff-330">Na [`@using`](xref:mvc/views/razor#using) součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují direktivu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="a7bff-331">`global::` Kvalifikace není podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a7bff-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="a7bff-332">Import komponent s `using` příkazy s aliasem (například `@using Foo = Bar`) není podporován.</span><span class="sxs-lookup"><span data-stu-id="a7bff-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="a7bff-333">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="a7bff-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="a7bff-334">Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="a7bff-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="a7bff-335">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="a7bff-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="a7bff-336">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="a7bff-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="a7bff-337">Pokud je `false` hodnota nebo `null`, atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="a7bff-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="a7bff-338">Pokud je `true`hodnota, je vygenerována hodnota atributu minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="a7bff-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="a7bff-339">V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="a7bff-340">Pokud `IsCompleted` je `true`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="a7bff-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="a7bff-341">Pokud `IsCompleted` je `false`, zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="a7bff-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="a7bff-342">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="a7bff-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="a7bff-343">Některé atributy HTML, jako je například [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nefungují správně, pokud je typ .NET `bool`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="a7bff-344">V těchto případech použijte `string` typ místo typu. `bool`</span><span class="sxs-lookup"><span data-stu-id="a7bff-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="a7bff-345">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="a7bff-345">Raw HTML</span></span>

<span data-ttu-id="a7bff-346">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="a7bff-347">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a7bff-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="a7bff-348">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="a7bff-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="a7bff-349">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="a7bff-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="a7bff-350">Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="a7bff-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="a7bff-351">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="a7bff-351">Cascading values and parameters</span></span>

<span data-ttu-id="a7bff-352">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="a7bff-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="a7bff-353">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="a7bff-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="a7bff-354">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="a7bff-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="a7bff-355">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="a7bff-355">Theme example</span></span>

<span data-ttu-id="a7bff-356">V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="a7bff-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="a7bff-357">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="a7bff-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="a7bff-358">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="a7bff-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="a7bff-359">`CascadingValue` Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="a7bff-360">Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a7bff-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="a7bff-361">`ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="a7bff-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="a7bff-362">Každá odvozená komponenta může tuto vlastnost spotřebovat `ThemeInfo` prostřednictvím kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="a7bff-363">`CascadingValuesParametersLayout`část</span><span class="sxs-lookup"><span data-stu-id="a7bff-363">`CascadingValuesParametersLayout` component:</span></span>

```razor
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

<span data-ttu-id="a7bff-364">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí `[CascadingParameter]` atributu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="a7bff-365">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="a7bff-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="a7bff-366">V ukázkové aplikaci váže `CascadingValuesParametersTheme` komponenta `ThemeInfo` kaskádovou hodnotu k kaskádovým parametrům.</span><span class="sxs-lookup"><span data-stu-id="a7bff-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="a7bff-367">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="a7bff-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="a7bff-368">`CascadingValuesParametersTheme`část</span><span class="sxs-lookup"><span data-stu-id="a7bff-368">`CascadingValuesParametersTheme` component:</span></span>

```razor
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

<span data-ttu-id="a7bff-369">Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` součást a odpovídající. `CascadingParameter`</span><span class="sxs-lookup"><span data-stu-id="a7bff-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="a7bff-370">V následujícím příkladu jsou dvě `CascadingValue` komponenty kaskádovitě různé instance `MyCascadingType` podle názvu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="a7bff-371">V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="a7bff-372">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="a7bff-372">TabSet example</span></span>

<span data-ttu-id="a7bff-373">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="a7bff-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="a7bff-374">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a7bff-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="a7bff-375">Ukázková aplikace má `ITab` rozhraní, které implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="a7bff-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="a7bff-376">`CascadingValuesParametersTabSet` Komponenta používá `TabSet` komponentu, která obsahuje několik `Tab` komponent:</span><span class="sxs-lookup"><span data-stu-id="a7bff-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="a7bff-377">Podřízené `Tab` součásti nejsou explicitně předány jako parametry do `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="a7bff-378">Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="a7bff-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="a7bff-379">`TabSet` Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu `TabSet` , komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta `Tab` podřízenými součástmi.</span><span class="sxs-lookup"><span data-stu-id="a7bff-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="a7bff-380">`TabSet`část</span><span class="sxs-lookup"><span data-stu-id="a7bff-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="a7bff-381">Podřízené komponenty `Tab` zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do souřadnice `TabSet` a, na které karty jsou aktivní.</span><span class="sxs-lookup"><span data-stu-id="a7bff-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="a7bff-382">`Tab`část</span><span class="sxs-lookup"><span data-stu-id="a7bff-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="a7bff-383">šablony</span><span class="sxs-lookup"><span data-stu-id="a7bff-383"> templates</span></span>

<span data-ttu-id="a7bff-384">Fragmenty vykreslování lze definovat pomocí Razor syntaxe šablony.</span><span class="sxs-lookup"><span data-stu-id="a7bff-384">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="a7bff-385">Šablony představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="a7bff-385"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="a7bff-386">Následující příklad ukazuje, jak zadat `RenderFragment` a `RenderFragment<T>` hodnoty a vykreslit šablony přímo v součásti.</span><span class="sxs-lookup"><span data-stu-id="a7bff-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="a7bff-387">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="a7bff-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="a7bff-388">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="a7bff-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="a7bff-389">Obrázky ve formátu SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="a7bff-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="a7bff-390">Vzhledem Blazor k `<img>` tomu, že vykresluje HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) (*. SVG*), jsou podporovány prostřednictvím značky:</span><span class="sxs-lookup"><span data-stu-id="a7bff-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="a7bff-391">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů (*. CSS*):</span><span class="sxs-lookup"><span data-stu-id="a7bff-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="a7bff-392">Vložené značky SVG se však ve všech scénářích nepodporují.</span><span class="sxs-lookup"><span data-stu-id="a7bff-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="a7bff-393">Pokud `<svg>` značku přímo umístíte do souboru komponenty (*. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů ještě není podporováno.</span><span class="sxs-lookup"><span data-stu-id="a7bff-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="a7bff-394">Například `<use>` značky nejsou aktuálně dodržovány a `@bind` nelze je použít s některými značkami SVG.</span><span class="sxs-lookup"><span data-stu-id="a7bff-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="a7bff-395">Očekáváme, že tato omezení vyřešíme v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="a7bff-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7bff-396">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a7bff-396">Additional resources</span></span>

* <span data-ttu-id="a7bff-397"><xref:security/blazor/server/threat-mitigation>&ndash; Obsahuje doprovodné materiály k Blazor vytváření serverových aplikací, které se musí soupeří s vyčerpáním prostředků.</span><span class="sxs-lookup"><span data-stu-id="a7bff-397"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
