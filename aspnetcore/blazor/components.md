---
title: Vytváření a používání ASP.NET Corech komponent Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak navazovat na data, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 7afc9250cdfb4b791ef939ead0f41b503d83fad8
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511272"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="56428-103">Vytváření a používání ASP.NET Corech komponent Razor</span><span class="sxs-lookup"><span data-stu-id="56428-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="56428-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="56428-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="56428-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56428-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="56428-106">aplikace Blazor jsou sestaveny pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="56428-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="56428-107">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="56428-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="56428-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="56428-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="56428-109">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="56428-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="56428-110">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="56428-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="56428-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="56428-111">Component classes</span></span>

<span data-ttu-id="56428-112">Komponenty jsou implementovány v souborech komponenty [Razor](xref:mvc/views/razor) ( *. Razor*) pomocí kombinace kódu C# a kódu HTML.</span><span class="sxs-lookup"><span data-stu-id="56428-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="56428-113">Komponenta v Blazor je formálně označována jako *Komponenta Razor*.</span><span class="sxs-lookup"><span data-stu-id="56428-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="56428-114">Název součásti musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="56428-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="56428-115">Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.</span><span class="sxs-lookup"><span data-stu-id="56428-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="56428-116">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="56428-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="56428-117">Dynamická logika vykreslování (například smyčky, podmíněné výrazy, výrazy) se přidá pomocí vložené C# syntaxe s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="56428-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="56428-118">Při kompilaci aplikace jsou značky kódu HTML a C# vykreslovací logiky převedeny na třídu součásti.</span><span class="sxs-lookup"><span data-stu-id="56428-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="56428-119">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="56428-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="56428-120">Členy třídy komponenty jsou definovány v `@code`ovém bloku.</span><span class="sxs-lookup"><span data-stu-id="56428-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="56428-121">V bloku `@code` je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="56428-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="56428-122">Je přípustný více než jeden blok `@code`.</span><span class="sxs-lookup"><span data-stu-id="56428-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="56428-123">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí C# výrazů, které začínají na `@`.</span><span class="sxs-lookup"><span data-stu-id="56428-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="56428-124">Například C# pole se vykreslí pomocí předpony `@` názvu pole.</span><span class="sxs-lookup"><span data-stu-id="56428-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="56428-125">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="56428-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="56428-126">`_headingFontStyle` k hodnotě vlastnosti CSS pro `font-style`.</span><span class="sxs-lookup"><span data-stu-id="56428-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="56428-127">`_headingText` k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="56428-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="56428-128">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="56428-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="56428-129"> pak porovná nový strom vykreslování s předchozí a použije všechny úpravy v prohlížeči model DOM (Document Object Model) (DOM).</span><span class="sxs-lookup"><span data-stu-id="56428-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="56428-130">Komponenty jsou běžné C# třídy a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="56428-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="56428-131">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="56428-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="56428-132">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="56428-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="56428-133">Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="56428-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="56428-134">Pokud je kořenový obor názvů aplikace `BlazorApp` a komponenta `Counter` se nachází ve složce *stránky* :</span><span class="sxs-lookup"><span data-stu-id="56428-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="56428-135">Obor názvů součásti `Counter` je `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="56428-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="56428-136">Plně kvalifikovaný název typu komponenty je `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="56428-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="56428-137">Další informace naleznete v části [Import komponent](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="56428-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="56428-138">Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky buď do nadřazené komponenty, nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="56428-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="56428-139">Například následující obor názvů zpřístupňuje komponenty ve složce *Components* , když je kořenový obor názvů aplikace `BlazorApp`:</span><span class="sxs-lookup"><span data-stu-id="56428-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="56428-140">Statické prostředky</span><span class="sxs-lookup"><span data-stu-id="56428-140">Static assets</span></span>

Blazor<span data-ttu-id="56428-141"> se řídí konvencí ASP.NET Core aplikací, které umísťují statické prostředky do [kořenové složky webu projektu (wwwroot)](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="56428-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="56428-142">K odkazování na webový kořenový adresář pro statický prostředek použijte cestu relativní vzhledem k základnímu prostředku (`/`).</span><span class="sxs-lookup"><span data-stu-id="56428-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="56428-143">V následujícím příkladu je soubor *logo. png* fyzicky umístěný ve složce *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="56428-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="56428-144">Komponenty Razor **nepodporují notaci** vlnovku (`~/`).</span><span class="sxs-lookup"><span data-stu-id="56428-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="56428-145">Informace o nastavení základní cesty aplikace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="56428-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="56428-146">V součástech nejsou podporovány pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="56428-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="56428-147">V součástech Razor (soubory *. Razor* ) nejsou podporované [pomocníky značek](xref:mvc/views/tag-helpers/intro) .</span><span class="sxs-lookup"><span data-stu-id="56428-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="56428-148">Chcete-li v Blazorposkytnout funkce podobné pomocníkům, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.</span><span class="sxs-lookup"><span data-stu-id="56428-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="56428-149">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="56428-149">Use components</span></span>

<span data-ttu-id="56428-150">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="56428-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="56428-151">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="56428-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="56428-152">Následující kód v *indexu. Razor* vykresluje instanci `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="56428-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="56428-153">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="56428-154">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="56428-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="56428-155">Přidání direktivy `@using` pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.</span><span class="sxs-lookup"><span data-stu-id="56428-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="56428-156">Směrování</span><span class="sxs-lookup"><span data-stu-id="56428-156">Routing</span></span>

<span data-ttu-id="56428-157">Směrování v Blazor dosáhnete tak, že v aplikaci poskytnete šablonu směrování pro každou dostupnou součást.</span><span class="sxs-lookup"><span data-stu-id="56428-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="56428-158">Když je zkompilován soubor Razor s direktivou `@page`, vygenerovaná třída má <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="56428-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="56428-159">V době běhu směrovač vyhledá třídy komponent pomocí `RouteAttribute` a vykreslí, že každá komponenta má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="56428-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="56428-160">Další informace naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="56428-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="56428-161">Parametry</span><span class="sxs-lookup"><span data-stu-id="56428-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="56428-162">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="56428-162">Route parameters</span></span>

<span data-ttu-id="56428-163">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v direktivě `@page`.</span><span class="sxs-lookup"><span data-stu-id="56428-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="56428-164">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="56428-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="56428-165">*Stránky/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="56428-166">Volitelné parametry nejsou podporované, takže se v předchozím příkladu použijí dvě direktivy `@page`.</span><span class="sxs-lookup"><span data-stu-id="56428-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="56428-167">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="56428-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="56428-168">Druhá direktiva `@page` přijímá parametr trasy `{text}` a přiřazuje hodnotu vlastnosti `Text`.</span><span class="sxs-lookup"><span data-stu-id="56428-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="56428-169">*Catch-All –* syntaxe parametru (`*`/`**`), která zachycuje cestu mezi více hranicemi složek, **není v** součástech Razor ( *. Razor*) podporována.</span><span class="sxs-lookup"><span data-stu-id="56428-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="56428-170">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="56428-170">Component parameters</span></span>

<span data-ttu-id="56428-171">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="56428-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="56428-172">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="56428-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="56428-173">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="56428-174">V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu vlastnosti `Title` `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="56428-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="56428-175">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="56428-176">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="56428-176">Child content</span></span>

<span data-ttu-id="56428-177">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="56428-177">Components can set the content of another component.</span></span> <span data-ttu-id="56428-178">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="56428-178">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="56428-179">V následujícím příkladu má `ChildComponent` vlastnost `ChildContent`, která představuje `RenderFragment`, která představuje segment uživatelského rozhraní pro vykreslení.</span><span class="sxs-lookup"><span data-stu-id="56428-179">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="56428-180">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="56428-180">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="56428-181">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v `panel-body`panelu Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="56428-181">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="56428-182">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-182">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="56428-183">Vlastnost, která přijímá obsah `RenderFragment`, musí mít název `ChildContent` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="56428-183">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="56428-184">`ParentComponent` v ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu do značek `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="56428-184">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="56428-185">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-185">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="56428-186">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="56428-186">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="56428-187">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="56428-187">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="56428-188">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí direktivy [`@attributes`](xref:mvc/views/razor#attributes) Razor.</span><span class="sxs-lookup"><span data-stu-id="56428-188">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="56428-189">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="56428-189">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="56428-190">Například může být zdlouhavé definovat atributy samostatně pro `<input>`, které podporují mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="56428-190">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="56428-191">V následujícím příkladu první `<input>` element (`id="useIndividualParams"`) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element (`id="useAttributesDict"`) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="56428-191">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="56428-192">Typ parametru musí implementovat `IEnumerable<KeyValuePair<string, object>>` s klíči řetězce.</span><span class="sxs-lookup"><span data-stu-id="56428-192">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="56428-193">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="56428-193">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="56428-194">Vykreslené `<input>` prvky pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="56428-194">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="56428-195">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí atributu `[Parameter]` s vlastností `CaptureUnmatchedValues` nastavenou na `true`:</span><span class="sxs-lookup"><span data-stu-id="56428-195">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="56428-196">Vlastnost `CaptureUnmatchedValues` v `[Parameter]` umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="56428-196">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="56428-197">Komponenta může definovat pouze jeden parametr s `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="56428-197">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="56428-198">Typ vlastnosti používaný u `CaptureUnmatchedValues` musí být možné přiřadit z `Dictionary<string, object>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="56428-198">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="56428-199">v tomto scénáři jsou také možnosti `IEnumerable<KeyValuePair<string, object>>` nebo `IReadOnlyDictionary<string, object>`.</span><span class="sxs-lookup"><span data-stu-id="56428-199">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="56428-200">Pozice `@attributes` relativní k pozici atributů elementu je důležitá.</span><span class="sxs-lookup"><span data-stu-id="56428-200">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="56428-201">Když `@attributes` jsou splattedy na elementu, atributy jsou zpracovávány zprava doleva (poslední až první).</span><span class="sxs-lookup"><span data-stu-id="56428-201">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="56428-202">Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:</span><span class="sxs-lookup"><span data-stu-id="56428-202">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="56428-203">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-203">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="56428-204">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-204">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="56428-205">Atribut `extra` `Child` komponenty je nastaven na hodnotu napravo od `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="56428-205">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="56428-206">`<div>` vykreslená komponenta `Parent` obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):</span><span class="sxs-lookup"><span data-stu-id="56428-206">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="56428-207">V následujícím příkladu je pořadí `extra` a `@attributes` v `<div>``Child` komponenty obrácené:</span><span class="sxs-lookup"><span data-stu-id="56428-207">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="56428-208">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-208">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="56428-209">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-209">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="56428-210">Vykreslený `<div>` v komponentě `Parent` obsahuje `extra="10"` při předání prostřednictvím dalšího atributu:</span><span class="sxs-lookup"><span data-stu-id="56428-210">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="56428-211">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="56428-211">Capture references to components</span></span>

<span data-ttu-id="56428-212">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset`.</span><span class="sxs-lookup"><span data-stu-id="56428-212">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="56428-213">Zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="56428-213">To capture a component reference:</span></span>

* <span data-ttu-id="56428-214">Přidejte atribut [`@ref`](xref:mvc/views/razor#ref) pro podřízenou komponentu.</span><span class="sxs-lookup"><span data-stu-id="56428-214">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="56428-215">Definujte pole stejného typu jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="56428-215">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="56428-216">Při vykreslení komponenty je pole `_loginDialog` vyplněno instancí `MyLoginDialog` podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="56428-216">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="56428-217">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="56428-217">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="56428-218">Proměnná `_loginDialog` je naplněna pouze po vykreslení komponenty a její výstup obsahuje prvek `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="56428-218">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="56428-219">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="56428-219">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="56428-220">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="56428-220">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="56428-221">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="56428-221">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="56428-222">Odkazy na součásti nejsou předány kódu jazyka JavaScript&mdash;jsou používány pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="56428-222">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="56428-223">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="56428-223">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="56428-224">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="56428-224">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="56428-225">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="56428-225">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="56428-226">Vyvolat metody komponenty externě na stav aktualizace</span><span class="sxs-lookup"><span data-stu-id="56428-226">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="56428-227"> používá `SynchronizationContext` k vykonání jediného logického vlákna provádění.</span><span class="sxs-lookup"><span data-stu-id="56428-227"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="56428-228">[Metody životního cyklu](xref:blazor/lifecycle) komponenty a všechna zpětná volání událostí, která jsou aktivována nástrojem Blazor, jsou spouštěna v tomto `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="56428-228">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="56428-229">V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte metodu `InvokeAsync`, která se odešle do `SynchronizationContext`Blazor.</span><span class="sxs-lookup"><span data-stu-id="56428-229">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="56428-230">Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:</span><span class="sxs-lookup"><span data-stu-id="56428-230">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="56428-231">Zaregistrujte `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="56428-231">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="56428-232">V Blazor WebAssembly Zaregistrujte službu v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="56428-232">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="56428-233">V Blazor serveru Zaregistrujte službu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="56428-233">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="56428-234">K aktualizaci součásti použijte `NotifierService`:</span><span class="sxs-lookup"><span data-stu-id="56428-234">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="56428-235">V předchozím příkladu `NotifierService` vyvolá metodu `OnNotify` komponenty mimo `SynchronizationContext`Blazor.</span><span class="sxs-lookup"><span data-stu-id="56428-235">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="56428-236">`InvokeAsync` slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.</span><span class="sxs-lookup"><span data-stu-id="56428-236">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="56428-237">Použití \@Key k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="56428-237">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="56428-238">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent musí být Blazorrozdílový algoritmus rozhodnout, které z předchozích prvků nebo komponent lze zachovat a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="56428-238">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="56428-239">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="56428-239">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="56428-240">Vezměte v úvahu v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="56428-240">Consider the following example:</span></span>

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

<span data-ttu-id="56428-241">Obsah kolekce `People` se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="56428-241">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="56428-242">Při revykreslování komponenty se může komponenta `<DetailsEditor>` změnit, aby přijímala jiné hodnoty parametrů `Details`.</span><span class="sxs-lookup"><span data-stu-id="56428-242">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="56428-243">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="56428-243">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="56428-244">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="56428-244">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="56428-245">Proces mapování lze řídit pomocí atributu direktivy `@key`.</span><span class="sxs-lookup"><span data-stu-id="56428-245">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="56428-246">`@key` způsobí, že rozdílový algoritmus zaručuje uchovávání prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="56428-246">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="56428-247">Když dojde ke změně kolekce `People`, rozdílový algoritmus zachovává přidružení mezi instancemi `<DetailsEditor>` a instancemi `person`:</span><span class="sxs-lookup"><span data-stu-id="56428-247">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="56428-248">Pokud se ze seznamu `People` odstraní `Person`, z uživatelského rozhraní se odebere jenom odpovídající instance `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="56428-248">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="56428-249">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="56428-249">Other instances are left unchanged.</span></span>
* <span data-ttu-id="56428-250">Pokud je na některém místě v seznamu vložená `Person`, do příslušné pozice se vloží jedna nová instance `<DetailsEditor>`.</span><span class="sxs-lookup"><span data-stu-id="56428-250">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="56428-251">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="56428-251">Other instances are left unchanged.</span></span>
* <span data-ttu-id="56428-252">Pokud jsou položky `Person` znovu seřazeny, odpovídající instance `<DetailsEditor>` jsou zachovány a v uživatelském rozhraní znovu seřazeny.</span><span class="sxs-lookup"><span data-stu-id="56428-252">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="56428-253">V některých scénářích použití `@key` minimalizuje složitost reprodukce a vyhnete se potenciálním problémům se měnícími se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="56428-253">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="56428-254">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="56428-254">Keys are local to each container element or component.</span></span> <span data-ttu-id="56428-255">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="56428-255">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="56428-256">Kdy použít klíč \@</span><span class="sxs-lookup"><span data-stu-id="56428-256">When to use \@key</span></span>

<span data-ttu-id="56428-257">Obvykle má smysl použít `@key` vždy, když je vygenerován seznam (například v bloku `@foreach`), a existuje vhodná hodnota pro definování `@key`.</span><span class="sxs-lookup"><span data-stu-id="56428-257">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="56428-258">Můžete také použít `@key` k zabránění Blazor při zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="56428-258">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="56428-259">Pokud `@currentPerson` změny, direktiva `@key` atributu vynutí Blazor zahodit celý `<div>` a jeho následníky a znovu sestavit podstrom v uživatelském rozhraní s novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="56428-259">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="56428-260">To může být užitečné, pokud potřebujete zaručit, že při `@currentPerson` změny se nezachová žádný stav uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="56428-260">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="56428-261">Kdy se \@klíč nepoužívá</span><span class="sxs-lookup"><span data-stu-id="56428-261">When not to use \@key</span></span>

<span data-ttu-id="56428-262">Při rozdílech s `@key`se účtují náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="56428-262">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="56428-263">Náklady na výkon nejsou velké, ale zadávejte `@key` jenom v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="56428-263">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="56428-264">I v případě, že `@key` nepoužívá, Blazor zachová podřízené prvky a instance komponent co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="56428-264">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="56428-265">Jedinou výhodou použití `@key` je řídit, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="56428-265">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="56428-266">Jaké hodnoty se mají použít pro klíč \@</span><span class="sxs-lookup"><span data-stu-id="56428-266">What values to use for \@key</span></span>

<span data-ttu-id="56428-267">Obecně je vhodné dodat jeden z následujících typů hodnoty pro `@key`:</span><span class="sxs-lookup"><span data-stu-id="56428-267">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="56428-268">Instance objektů modelu (například instance `Person` jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="56428-268">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="56428-269">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="56428-269">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="56428-270">Jedinečné identifikátory (například hodnoty primárního klíče typu `int`, `string`nebo `Guid`).</span><span class="sxs-lookup"><span data-stu-id="56428-270">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="56428-271">Zajistěte, aby hodnoty používané pro `@key` nekolidovat.</span><span class="sxs-lookup"><span data-stu-id="56428-271">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="56428-272">Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty konfliktu, Blazor vyvolá výjimku, protože nemůže deterministickém mapovat staré prvky nebo komponenty na nové prvky nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="56428-272">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="56428-273">Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="56428-273">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="56428-274">Podpora částečné třídy</span><span class="sxs-lookup"><span data-stu-id="56428-274">Partial class support</span></span>

<span data-ttu-id="56428-275">Komponenty Razor jsou generovány jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="56428-275">Razor components are generated as partial classes.</span></span> <span data-ttu-id="56428-276">Komponenty Razor jsou vytvořeny některým z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="56428-276">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="56428-277">C#kód je definován v [`@code`](xref:mvc/views/razor#code) bloku pomocí značek HTML a kódu Razor v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="56428-277">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="56428-278">šablony Blazor definují své komponenty Razor pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="56428-278">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="56428-279">C#kód je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.</span><span class="sxs-lookup"><span data-stu-id="56428-279">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="56428-280">Následující příklad ukazuje výchozí komponentu `Counter` s blokem `@code` v aplikaci vygenerovanou šablonou Blazor.</span><span class="sxs-lookup"><span data-stu-id="56428-280">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="56428-281">Značky HTML, kód Razor a C# kód jsou ve stejném souboru:</span><span class="sxs-lookup"><span data-stu-id="56428-281">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="56428-282">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-282">*Counter.razor*:</span></span>

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

<span data-ttu-id="56428-283">Komponentu `Counter` lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:</span><span class="sxs-lookup"><span data-stu-id="56428-283">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="56428-284">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-284">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="56428-285">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="56428-285">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="56428-286">Podle potřeby přidejte všechny požadované obory názvů do souboru dílčí třídy.</span><span class="sxs-lookup"><span data-stu-id="56428-286">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="56428-287">Mezi obvyklé obory názvů používané komponentami Razor patří:</span><span class="sxs-lookup"><span data-stu-id="56428-287">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="56428-288">Zadat základní třídu</span><span class="sxs-lookup"><span data-stu-id="56428-288">Specify a base class</span></span>

<span data-ttu-id="56428-289">Direktivu [`@inherits`](xref:mvc/views/razor#inherits) lze použít k určení základní třídy pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="56428-289">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="56428-290">Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase`, k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="56428-290">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="56428-291">Základní třída by měla být odvozena od `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="56428-291">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="56428-292">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="56428-292">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="56428-293">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="56428-293">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="56428-294">Zadat atribut</span><span class="sxs-lookup"><span data-stu-id="56428-294">Specify an attribute</span></span>

<span data-ttu-id="56428-295">Atributy lze zadat v součástech Razor s direktivou [`@attribute`](xref:mvc/views/razor#attribute) .</span><span class="sxs-lookup"><span data-stu-id="56428-295">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="56428-296">Následující příklad používá atribut `[Authorize]` pro třídu komponenty:</span><span class="sxs-lookup"><span data-stu-id="56428-296">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="56428-297">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="56428-297">Import components</span></span>

<span data-ttu-id="56428-298">Obor názvů komponenty, která je vytvořená pomocí Razor, je založen na (v pořadí podle priority):</span><span class="sxs-lookup"><span data-stu-id="56428-298">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="56428-299">[`@namespace`](xref:mvc/views/razor#namespace) označení v souboru Razor ( *. Razor*) značky (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="56428-299">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="56428-300">`RootNamespace` projektu v souboru projektu (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="56428-300">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="56428-301">Název projektu, pořízený z názvu souboru projektu ( *. csproj*) a cesta z kořenového adresáře projektu ke komponentě.</span><span class="sxs-lookup"><span data-stu-id="56428-301">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="56428-302">Například rozhraní řeší *{Project root}/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="56428-302">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="56428-303">Komponenty následují C# pravidla vazeb názvů.</span><span class="sxs-lookup"><span data-stu-id="56428-303">Components follow C# name binding rules.</span></span> <span data-ttu-id="56428-304">Pro komponentu `Index` v tomto příkladu komponenty v oboru jsou všechny komponenty:</span><span class="sxs-lookup"><span data-stu-id="56428-304">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="56428-305">Ve stejné složce *stránky*.</span><span class="sxs-lookup"><span data-stu-id="56428-305">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="56428-306">Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.</span><span class="sxs-lookup"><span data-stu-id="56428-306">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="56428-307">Komponenty definované v jiném oboru názvů se přenesou do rozsahu pomocí direktivy [`@using`](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="56428-307">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="56428-308">Pokud v *BlazorSample/Shared/* Folder existuje jiná komponenta, `NavMenu.razor`, lze komponentu použít v `Index.razor` pomocí následujícího příkazu `@using`:</span><span class="sxs-lookup"><span data-stu-id="56428-308">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="56428-309">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují direktivu [`@using`](xref:mvc/views/razor#using) :</span><span class="sxs-lookup"><span data-stu-id="56428-309">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="56428-310">Kvalifikace `global::` není podporována.</span><span class="sxs-lookup"><span data-stu-id="56428-310">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="56428-311">Import komponent s aliasy `using` příkazy (například `@using Foo = Bar`) není podporován.</span><span class="sxs-lookup"><span data-stu-id="56428-311">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="56428-312">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="56428-312">Partially qualified names aren't supported.</span></span> <span data-ttu-id="56428-313">Například přidání `@using BlazorSample` a odkazování `NavMenu.razor` pomocí `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="56428-313">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="56428-314">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="56428-314">Conditional HTML element attributes</span></span>

<span data-ttu-id="56428-315">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="56428-315">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="56428-316">Pokud je hodnota `false` nebo `null`, není atribut vykreslen.</span><span class="sxs-lookup"><span data-stu-id="56428-316">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="56428-317">Pokud je hodnota `true`, vykreslí se atribut jako minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="56428-317">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="56428-318">V následujícím příkladu `IsCompleted` určuje, zda je `checked` vykreslen v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="56428-318">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="56428-319">Pokud je `IsCompleted` `true`, zaškrtávací políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="56428-319">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="56428-320">Pokud je `IsCompleted` `false`, zaškrtávací políčko se vykreslí jako:</span><span class="sxs-lookup"><span data-stu-id="56428-320">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="56428-321">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="56428-321">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="56428-322">Některé atributy HTML, jako je například [Standard ARIA](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nebudou fungovat správně, pokud je typ .NET `bool`.</span><span class="sxs-lookup"><span data-stu-id="56428-322">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="56428-323">V těchto případech použijte místo `bool``string` typ.</span><span class="sxs-lookup"><span data-stu-id="56428-323">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="56428-324">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="56428-324">Raw HTML</span></span>

<span data-ttu-id="56428-325">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="56428-325">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="56428-326">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="56428-326">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="56428-327">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="56428-327">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="56428-328">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="56428-328">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="56428-329">Následující příklad ukazuje použití typu `MarkupString` pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="56428-329">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="56428-330">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="56428-330">Cascading values and parameters</span></span>

<span data-ttu-id="56428-331">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="56428-331">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="56428-332">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="56428-332">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="56428-333">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="56428-333">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="56428-334">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="56428-334">Theme example</span></span>

<span data-ttu-id="56428-335">V následujícím příkladu z ukázkové aplikace určuje třída `ThemeInfo` informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="56428-335">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="56428-336">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="56428-336">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="56428-337">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="56428-337">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="56428-338">Komponenta `CascadingValue` zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="56428-338">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="56428-339">Například ukázková aplikace určuje informace o motivu (`ThemeInfo`) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří text rozložení `@Body` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="56428-339">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="56428-340">`ButtonClass` je v komponentě rozložení přiřazena hodnota `btn-success`.</span><span class="sxs-lookup"><span data-stu-id="56428-340">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="56428-341">Jakákoli následná komponenta může tuto vlastnost spotřebovat prostřednictvím `ThemeInfo` kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="56428-341">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="56428-342">součást `CascadingValuesParametersLayout`:</span><span class="sxs-lookup"><span data-stu-id="56428-342">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="56428-343">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí atributu `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="56428-343">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="56428-344">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="56428-344">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="56428-345">V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta váže kaskádovou hodnotu `ThemeInfo` na kaskádový parametr.</span><span class="sxs-lookup"><span data-stu-id="56428-345">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="56428-346">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="56428-346">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="56428-347">součást `CascadingValuesParametersTheme`:</span><span class="sxs-lookup"><span data-stu-id="56428-347">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="56428-348">Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný `Name` řetězec pro každou `CascadingValue` komponentu a odpovídající `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="56428-348">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="56428-349">V následujícím příkladu se dvě `CascadingValue` komponenty zanášejí do různých instancí `MyCascadingType` podle názvu:</span><span class="sxs-lookup"><span data-stu-id="56428-349">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="56428-350">V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="56428-350">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="56428-351">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="56428-351">TabSet example</span></span>

<span data-ttu-id="56428-352">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="56428-352">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="56428-353">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="56428-353">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="56428-354">Ukázková aplikace má `ITab` rozhraní, které tyto karty implementují:</span><span class="sxs-lookup"><span data-stu-id="56428-354">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="56428-355">Komponenta `CascadingValuesParametersTabSet` používá součást `TabSet`, která obsahuje několik `Tab` komponent:</span><span class="sxs-lookup"><span data-stu-id="56428-355">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="56428-356">Podřízené `Tab` komponenty nejsou explicitně předány jako parametry `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="56428-356">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="56428-357">Místo toho jsou podřízené součásti `Tab` součástí podřízeného obsahu `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="56428-357">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="56428-358">`TabSet` však stále potřebuje znát každou součást `Tab`, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, komponenta `TabSet` *může poskytnout sebe sama jako kaskádovou hodnotu* , která je poté odebrána pomocí podřízených `Tab` komponent.</span><span class="sxs-lookup"><span data-stu-id="56428-358">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="56428-359">součást `TabSet`:</span><span class="sxs-lookup"><span data-stu-id="56428-359">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="56428-360">Podřízené `Tab` komponenty zachytí obsahující `TabSet` jako kaskádový parametr, takže součásti `Tab` přidají sebe do `TabSet` a koordinují, na které kartě je aktivní.</span><span class="sxs-lookup"><span data-stu-id="56428-360">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="56428-361">součást `Tab`:</span><span class="sxs-lookup"><span data-stu-id="56428-361">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="56428-362">Šablony Razor</span><span class="sxs-lookup"><span data-stu-id="56428-362">Razor templates</span></span>

<span data-ttu-id="56428-363">Fragmenty vykreslování lze definovat pomocí syntaxe šablony Razor.</span><span class="sxs-lookup"><span data-stu-id="56428-363">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="56428-364">Šablony Razor představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="56428-364">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="56428-365">Následující příklad ukazuje, jak určit hodnoty `RenderFragment` a `RenderFragment<T>` a vykreslit šablony přímo v komponentě.</span><span class="sxs-lookup"><span data-stu-id="56428-365">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="56428-366">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="56428-366">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="56428-367">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="56428-367">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="56428-368">Obrázky ve formátu SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="56428-368">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="56428-369">Vzhledem k tomu, že Blazor vykresluje HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) ( *. SVG*), jsou podporovány prostřednictvím značky `<img>`:</span><span class="sxs-lookup"><span data-stu-id="56428-369">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="56428-370">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů ( *. CSS*):</span><span class="sxs-lookup"><span data-stu-id="56428-370">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="56428-371">Vložené značky SVG se však ve všech scénářích nepodporují.</span><span class="sxs-lookup"><span data-stu-id="56428-371">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="56428-372">Pokud značku `<svg>` umístíte přímo do souboru komponenty ( *. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů se ještě nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="56428-372">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="56428-373">Například `<use>` značky nejsou aktuálně dodrženy a `@bind` nelze použít s některými značkami SVG.</span><span class="sxs-lookup"><span data-stu-id="56428-373">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="56428-374">Očekáváme, že tato omezení vyřešíme v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="56428-374">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56428-375">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="56428-375">Additional resources</span></span>

* <span data-ttu-id="56428-376"><xref:security/blazor/server> &ndash; obsahuje pokyny k vytváření Blazor serverových aplikací, které se musí soupeří s vyčerpáním prostředků.</span><span class="sxs-lookup"><span data-stu-id="56428-376"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
