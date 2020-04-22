---
title: Vytvářejte a používejte komponenty ASP.NET Core Razor
author: guardrex
description: Naučte se vytvářet a používat komponenty Razor, včetně toho, jak svázat s daty, zpracovávat události a spravovat životní cykly komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/21/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: 4434636992cb2506ef6525996690946f97c43764
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791491"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="a5731-103">Vytvářejte a používejte komponenty ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="a5731-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="a5731-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="a5731-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="a5731-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="a5731-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="a5731-106">aplikace jsou vytvořeny pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="a5731-106"> apps are built using *components*.</span></span> <span data-ttu-id="a5731-107">Komponenta je samostatný blok uživatelského rozhraní (UI), například stránka, dialog nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="a5731-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="a5731-108">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vložení dat nebo reakci na události nového jazyka.</span><span class="sxs-lookup"><span data-stu-id="a5731-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="a5731-109">Komponenty jsou flexibilní a lehké.</span><span class="sxs-lookup"><span data-stu-id="a5731-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="a5731-110">Lze je vnořit, znovu použít a sdílet mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="a5731-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="a5731-111">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="a5731-111">Component classes</span></span>

<span data-ttu-id="a5731-112">Komponenty jsou implementovány v souborech komponent [Razor](xref:mvc/views/razor) (*.razor*) pomocí kombinace značek C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="a5731-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="a5731-113">Součást in Blazor je formálně označována jako *součást Razor*.</span><span class="sxs-lookup"><span data-stu-id="a5731-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="a5731-114">Název komponenty musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="a5731-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="a5731-115">Například *MyCoolComponent.razor* je platný a *myCoolComponent.razor* je neplatný.</span><span class="sxs-lookup"><span data-stu-id="a5731-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="a5731-116">UI pro komponentu je definováno pomocí HTML.</span><span class="sxs-lookup"><span data-stu-id="a5731-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="a5731-117">Logika dynamického vykreslování (například smyčky, podmínky, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="a5731-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="a5731-118">Když je aplikace zkompilován, kódy HTML a logika vykreslování Jazyka C# jsou převedeny na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="a5731-119">Název generované třídy odpovídá názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="a5731-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="a5731-120">Členové třídy komponent jsou `@code` definováni v bloku.</span><span class="sxs-lookup"><span data-stu-id="a5731-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="a5731-121">V `@code` bloku je stav komponenty (vlastnosti, pole) určen metodami pro zpracování událostí nebo pro definování jiné logiky komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="a5731-122">Je přípustné `@code` více než jeden blok.</span><span class="sxs-lookup"><span data-stu-id="a5731-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="a5731-123">Členy komponenty lze použít jako součást logiky vykreslování komponenty pomocí výrazů Jazyka C#, které začínají písmenem `@`.</span><span class="sxs-lookup"><span data-stu-id="a5731-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="a5731-124">Například c# pole je vykreslen předponou `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="a5731-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="a5731-125">Následující příklad vyhodnotí a vykreslí:</span><span class="sxs-lookup"><span data-stu-id="a5731-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="a5731-126">`_headingFontStyle`na hodnotu vlastnosti `font-style`CSS pro .</span><span class="sxs-lookup"><span data-stu-id="a5731-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="a5731-127">`_headingText`k obsahu `<h1>` prvku.</span><span class="sxs-lookup"><span data-stu-id="a5731-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="a5731-128">Po zpočátku vykreslování komponenty komponenty regeneruje svůj strom vykreslení v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="a5731-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="a5731-129">potom porovná nový strom vykreslení s předchozím stromem a použije všechny změny v objektovém modelu dokumentu prohlížeče (DOM).</span><span class="sxs-lookup"><span data-stu-id="a5731-129"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="a5731-130">Součásti jsou běžné třídy Jazyka C# a lze umístit kdekoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="a5731-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="a5731-131">Součásti, které vytvářejí webové stránky, jsou obvykle umístěny ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="a5731-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="a5731-132">Nestránkové součásti jsou často umístěny ve *sdílené* složce nebo ve vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="a5731-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="a5731-133">Obor názvů komponenty je obvykle odvozen od kořenového oboru názvů aplikace a umístění (složky) komponenty v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5731-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="a5731-134">Pokud je `BlazorApp` kořenový obor názvů `Counter` aplikace a komponenta se nachází ve složce *Stránky:*</span><span class="sxs-lookup"><span data-stu-id="a5731-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="a5731-135">Obor `Counter` názvů komponenty `BlazorApp.Pages`je .</span><span class="sxs-lookup"><span data-stu-id="a5731-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="a5731-136">Plně kvalifikovaný název typu součásti je `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="a5731-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="a5731-137">Další informace naleznete v části [Importovat součásti.](#import-components)</span><span class="sxs-lookup"><span data-stu-id="a5731-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="a5731-138">Chcete-li použít vlastní složku, přidejte obor názvů vlastní složky do nadřazené součásti nebo do souboru *_Imports.razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5731-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="a5731-139">Například následující obor názvů zpřístupňuje součásti ve složce *Komponenty,* `BlazorApp`pokud je kořenový obor názvů aplikace :</span><span class="sxs-lookup"><span data-stu-id="a5731-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a><span data-ttu-id="a5731-140">Statické datové zdroje</span><span class="sxs-lookup"><span data-stu-id="a5731-140">Static assets</span></span>

Blazor<span data-ttu-id="a5731-141">řídí se konvencí aplikací ASP.NET Core, které umísťují statické datové zdroje pod [složku kořenového adresáře webu (wwwroot) projektu](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a5731-141"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="a5731-142">Použijte cestu relativní k`/`základně ( ) k odkazování na kořen webu pro statický datový zdroj.</span><span class="sxs-lookup"><span data-stu-id="a5731-142">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="a5731-143">V následujícím příkladu je *soubor logo.png* fyzicky umístěn ve složce *{PROJECT ROOT}/wwwroot/images:*</span><span class="sxs-lookup"><span data-stu-id="a5731-143">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="a5731-144">Komponenty břitvy **nepodporují** zápis tildového lomítka (`~/`).</span><span class="sxs-lookup"><span data-stu-id="a5731-144">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="a5731-145">Informace o nastavení základní cesty aplikace <xref:host-and-deploy/blazor/index#app-base-path>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="a5731-145">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="a5731-146">Pomocné spoje nejsou v součástech podporovány</span><span class="sxs-lookup"><span data-stu-id="a5731-146">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="a5731-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) nejsou podporovány v razor komponenty *(.razor* soubory).</span><span class="sxs-lookup"><span data-stu-id="a5731-147">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="a5731-148">Chcete-li v části Poskytnout Blazorfunkci podobné funkci pomocné značce , vytvořte komponentu se stejnou funkcí jako pomocník značek a místo toho použijte komponentu.</span><span class="sxs-lookup"><span data-stu-id="a5731-148">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="a5731-149">Použití součástí</span><span class="sxs-lookup"><span data-stu-id="a5731-149">Use components</span></span>

<span data-ttu-id="a5731-150">Součásti mohou obsahovat další součásti deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="a5731-150">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="a5731-151">Značky pro použití komponenty vypadají jako značka HTML, kde je název značky typem komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-151">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="a5731-152">Následující značky v *Index.razor* `HeadingComponent` vykreslí instanci:</span><span class="sxs-lookup"><span data-stu-id="a5731-152">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="a5731-153">*Komponenty/HeadingComponent.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="a5731-153">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="a5731-154">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vyzařováno upozornění označující, že prvek má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="a5731-154">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="a5731-155">Přidání `@using` směrnice pro obor názvů komponenty zpřístupní součást, která řeší upozornění.</span><span class="sxs-lookup"><span data-stu-id="a5731-155">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="a5731-156">Směrování</span><span class="sxs-lookup"><span data-stu-id="a5731-156">Routing</span></span>

<span data-ttu-id="a5731-157">Směrování Blazor v je dosaženo poskytnutím šablony trasy pro každou přístupnou součást v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a5731-157">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="a5731-158">Při kompilaci souboru Razor se `@page` směrnicí je vygenerovaná třída přidělena <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> určující šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="a5731-158">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="a5731-159">Za běhu směrovač vyhledá třídy komponent `RouteAttribute` s a vykreslí, která komponenta má šablonu trasy, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="a5731-159">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="a5731-160">Další informace naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="a5731-160">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="a5731-161">Parametry</span><span class="sxs-lookup"><span data-stu-id="a5731-161">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="a5731-162">Parametry trasy</span><span class="sxs-lookup"><span data-stu-id="a5731-162">Route parameters</span></span>

<span data-ttu-id="a5731-163">Komponenty mohou přijímat parametry postupu ze šablony `@page` postupu uvedené ve směrnici.</span><span class="sxs-lookup"><span data-stu-id="a5731-163">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="a5731-164">Směrovač používá parametry trasy k naplnění odpovídajících parametrů komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-164">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="a5731-165">*Stránky/RouteParameter.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="a5731-165">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="a5731-166">Volitelné parametry nejsou podporovány, `@page` takže v předchozím příkladu jsou použity dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="a5731-166">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="a5731-167">První umožňuje navigaci do komponenty bez parametru.</span><span class="sxs-lookup"><span data-stu-id="a5731-167">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="a5731-168">Druhá `@page` směrnice obdrží `{text}` parametr trasy a přiřadí `Text` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a5731-168">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="a5731-169">*Syntaxe parametrů catch-all* `*`/`**`( ), která zachycuje cestu přes hranice více složek, **není** podporována v komponentách Razor (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="a5731-169">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="a5731-170">Parametry komponenty</span><span class="sxs-lookup"><span data-stu-id="a5731-170">Component parameters</span></span>

<span data-ttu-id="a5731-171">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy komponenty s atributem. `[Parameter]`</span><span class="sxs-lookup"><span data-stu-id="a5731-171">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="a5731-172">Pomocí atributů můžete zadat argumenty pro komponentu ve značkách.</span><span class="sxs-lookup"><span data-stu-id="a5731-172">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="a5731-173">*Komponenty/ChildComponent.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="a5731-173">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="a5731-174">V následujícím příkladu z ukázkové aplikace nastaví `ParentComponent` hodnotu `Title` vlastnosti `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="a5731-174">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="a5731-175">*Stránky/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5731-175">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="a5731-176">Nevytvářejte součásti, které zapisují do *vlastních parametrů komponenty*, použijte místo toho soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="a5731-176">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="a5731-177">Další informace naleznete v části [Nevytvářet součásti, které zapisují do vlastních vlastností parametrů.](#dont-create-components-that-write-to-their-own-parameter-properties)</span><span class="sxs-lookup"><span data-stu-id="a5731-177">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="a5731-178">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="a5731-178">Child content</span></span>

<span data-ttu-id="a5731-179">Součásti mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="a5731-179">Components can set the content of another component.</span></span> <span data-ttu-id="a5731-180">Přiřazující komponenta poskytuje obsah mezi značkami, které určují přijímající komponentu.</span><span class="sxs-lookup"><span data-stu-id="a5731-180">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="a5731-181">V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, `RenderFragment`která představuje , který představuje segment uI k vykreslení.</span><span class="sxs-lookup"><span data-stu-id="a5731-181">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="a5731-182">Hodnota `ChildContent` je umístěna ve značkách komponenty, kde by měl být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="a5731-182">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="a5731-183">Hodnota `ChildContent` je přijata z nadřazené součásti a `panel-body`vykreslena uvnitř panelu Bootstrap .</span><span class="sxs-lookup"><span data-stu-id="a5731-183">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="a5731-184">*Komponenty/ChildComponent.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="a5731-184">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="a5731-185">Vlastnost přijímající `RenderFragment` obsah musí `ChildContent` být pojmenována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="a5731-185">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="a5731-186">V `ParentComponent` ukázkové aplikaci můžete poskytnout `ChildComponent` obsah pro vykreslování umístěním obsahu uvnitř `<ChildComponent>` značek.</span><span class="sxs-lookup"><span data-stu-id="a5731-186">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="a5731-187">*Stránky/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5731-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="a5731-188">Atribut splatting a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="a5731-188">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="a5731-189">Součásti mohou zachytit a vykreslit další atributy kromě deklarovaných parametrů komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-189">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="a5731-190">Další atributy mohou být zachyceny ve slovníku a potom *přetavovány* na [`@attributes`](xref:mvc/views/razor#attributes) prvek při vykreslení komponenty pomocí direktivy Razor.</span><span class="sxs-lookup"><span data-stu-id="a5731-190">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="a5731-191">Tento scénář je užitečné při definování součásti, která vytváří prvek značky, který podporuje různé vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="a5731-191">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="a5731-192">Například může být únavné definovat atributy samostatně pro `<input>` který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="a5731-192">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="a5731-193">V následujícím příkladu `<input>` první`id="useIndividualParams"`prvek ( ) používá parametry `<input>` jednotlivých komponent, zatímco druhý prvek (`id="useAttributesDict"`) používá atribut splatting:</span><span class="sxs-lookup"><span data-stu-id="a5731-193">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="a5731-194">Typ parametru musí `IEnumerable<KeyValuePair<string, object>>` implementovat s řetězcovými klíči.</span><span class="sxs-lookup"><span data-stu-id="a5731-194">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="a5731-195">Použití `IReadOnlyDictionary<string, object>` je také možnost v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="a5731-195">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="a5731-196">Vykreslené `<input>` prvky pomocí obou přístupů je identická:</span><span class="sxs-lookup"><span data-stu-id="a5731-196">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="a5731-197">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí atributu `[Parameter]` s vlastností nastavenou `CaptureUnmatchedValues` na `true`:</span><span class="sxs-lookup"><span data-stu-id="a5731-197">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="a5731-198">Vlastnost `CaptureUnmatchedValues` na `[Parameter]` umožňuje parametr, aby odpovídaly všechny atributy, které neodpovídají žádné jiné parametr.</span><span class="sxs-lookup"><span data-stu-id="a5731-198">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="a5731-199">Komponenta může definovat pouze `CaptureUnmatchedValues`jeden parametr s .</span><span class="sxs-lookup"><span data-stu-id="a5731-199">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="a5731-200">Typ vlastnosti, `CaptureUnmatchedValues` který se `Dictionary<string, object>` používá s musí být přiřaditelné z s řetězci klíče.</span><span class="sxs-lookup"><span data-stu-id="a5731-200">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="a5731-201">`IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="a5731-201">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="a5731-202">Pozice vzhledem `@attributes` k pozici atributů prvku je důležitá.</span><span class="sxs-lookup"><span data-stu-id="a5731-202">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="a5731-203">Když `@attributes` jsou štěkat na prvek, atributy jsou zpracovány zprava doleva (poslední k prvnímu).</span><span class="sxs-lookup"><span data-stu-id="a5731-203">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="a5731-204">Zvažte následující příklad součásti, `Child` která spotřebovává komponentu:</span><span class="sxs-lookup"><span data-stu-id="a5731-204">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="a5731-205">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5731-205">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="a5731-206">*ChildComponent.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="a5731-206">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="a5731-207">Atribut `Child` komponenty `extra` je nastaven na `@attributes`pravo .</span><span class="sxs-lookup"><span data-stu-id="a5731-207">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="a5731-208">Vykreslená `Parent` komponenta `<div>` obsahuje `extra="5"` při průchodu další atribut, protože atributy jsou zpracovány zprava doleva (od poslední k první):</span><span class="sxs-lookup"><span data-stu-id="a5731-208">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="a5731-209">V následujícím příkladu je `extra` `@attributes` pořadí a `Child` je obráceno v komponentě `<div>`:</span><span class="sxs-lookup"><span data-stu-id="a5731-209">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="a5731-210">*ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5731-210">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="a5731-211">*ChildComponent.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="a5731-211">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="a5731-212">Vykreslenv `<div>` komponenty `Parent` obsahuje `extra="10"` při průchodu další atribut:</span><span class="sxs-lookup"><span data-stu-id="a5731-212">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="a5731-213">Zachycení odkazů na součásti</span><span class="sxs-lookup"><span data-stu-id="a5731-213">Capture references to components</span></span>

<span data-ttu-id="a5731-214">Odkazy na komponenty umožňují odkazovat na instanci komponenty, takže `Show` můžete `Reset`vydávat příkazy k této instanci, například nebo .</span><span class="sxs-lookup"><span data-stu-id="a5731-214">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="a5731-215">Chcete-li zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="a5731-215">To capture a component reference:</span></span>

* <span data-ttu-id="a5731-216">Přidejte [`@ref`](xref:mvc/views/razor#ref) atribut do podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="a5731-216">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="a5731-217">Definujte pole se stejným typem jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="a5731-217">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="a5731-218">Při vykreslení komponenty `_loginDialog` je pole naplněno `MyLoginDialog` instancí podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="a5731-218">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="a5731-219">Potom můžete vyvolat metody .NET na instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-219">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a5731-220">Proměnná `_loginDialog` je naplněna pouze po vykreslení komponenty `MyLoginDialog` a její výstup obsahuje prvek.</span><span class="sxs-lookup"><span data-stu-id="a5731-220">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="a5731-221">Do té doby není na co se odkazovat.</span><span class="sxs-lookup"><span data-stu-id="a5731-221">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="a5731-222">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování součásti, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="a5731-222">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="a5731-223">Chcete-li odkazovat na součásti ve smyčce, viz [Zachycení odkazů na více podobných podřízených součástí (dotnet/aspnetcore #13358).](https://github.com/dotnet/aspnetcore/issues/13358)</span><span class="sxs-lookup"><span data-stu-id="a5731-223">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="a5731-224">Při zachytávání odkazů na komponenty používá podobnou syntaxi k [zachytávání odkazů na elementy](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), nejedná se o funkci interop javascriptu.</span><span class="sxs-lookup"><span data-stu-id="a5731-224">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="a5731-225">Odkazy na komponenty nejsou předány&mdash;kódu JavaScriptu, které se používají pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="a5731-225">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="a5731-226">**Nepoužívejte** odkazy na komponenty k mutu stavu podřízených součástí.</span><span class="sxs-lookup"><span data-stu-id="a5731-226">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="a5731-227">Místo toho použijte normální deklarativní parametry k předání dat podřízeným součástem.</span><span class="sxs-lookup"><span data-stu-id="a5731-227">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="a5731-228">Použití normálních deklarativních parametrů má za následek podřízené součásti, které se automaticky vykreslují ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="a5731-228">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="a5731-229">Vyvolat metody komponenty externě aktualizovat stav</span><span class="sxs-lookup"><span data-stu-id="a5731-229">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="a5731-230">používá `SynchronizationContext` k vynucení jednoho logického vlákna provádění.</span><span class="sxs-lookup"><span data-stu-id="a5731-230"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="a5731-231">Metody životního [cyklu komponenty](xref:blazor/lifecycle) a všechna zpětná volání Blazor událostí, která `SynchronizationContext`jsou vyvolána, jsou v tomto spuštěny .</span><span class="sxs-lookup"><span data-stu-id="a5731-231">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="a5731-232">V případě, že komponenta musí být aktualizována na základě externí události, `InvokeAsync` jako je například časovač nebo jiná oznámení, použijte metodu, která bude odeslána do Blazoraplikace `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="a5731-232">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="a5731-233">Zvažte například *službu oznamovatele,* která může upozornit jakoukoli součást naslouchání aktualizovaného stavu:</span><span class="sxs-lookup"><span data-stu-id="a5731-233">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="a5731-234">Zaregistrujte `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="a5731-234">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="a5731-235">Ve Blazor službě WebAssembly zaregistrujte službu v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a5731-235">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="a5731-236">V Blazor části Server zaregistrujte službu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a5731-236">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="a5731-237">Použijte `NotifierService` k aktualizaci komponenty:</span><span class="sxs-lookup"><span data-stu-id="a5731-237">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="a5731-238">V předchozím příkladu `NotifierService` vyvolá `OnNotify` metodu komponenty Blazormimo `SynchronizationContext`'s .</span><span class="sxs-lookup"><span data-stu-id="a5731-238">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="a5731-239">`InvokeAsync`slouží k přepnutí do správného kontextu a fronty vykreslení.</span><span class="sxs-lookup"><span data-stu-id="a5731-239">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="a5731-240">Použití \@klíče k řízení zachování prvků a součástí</span><span class="sxs-lookup"><span data-stu-id="a5731-240">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="a5731-241">Při vykreslování seznam prvků nebo součástí a Blazorprvky nebo součásti následně změnit, 's diffing algoritmus musí rozhodnout, které z předchozích prvků nebo součástí mohou být zachovány a jak by měly být objekty modelu mapovat na ně.</span><span class="sxs-lookup"><span data-stu-id="a5731-241">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="a5731-242">Za normálních okolností je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="a5731-242">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="a5731-243">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="a5731-243">Consider the following example:</span></span>

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

<span data-ttu-id="a5731-244">Obsah kolekce `People` se může změnit s vloženými, odstraněnými nebo znovu objednanými položkami.</span><span class="sxs-lookup"><span data-stu-id="a5731-244">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="a5731-245">Když se komponenta znovu `<DetailsEditor>` vykreslí, `Details` může se změnit, aby přijínala různé hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="a5731-245">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="a5731-246">To může způsobit složitější rerendering, než bylo očekáváno.</span><span class="sxs-lookup"><span data-stu-id="a5731-246">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="a5731-247">V některých případech může rerendering vést k viditelné rozdíly chování, jako je například ztráta fokusu prvku.</span><span class="sxs-lookup"><span data-stu-id="a5731-247">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="a5731-248">Proces mapování lze řídit [`@key`](xref:mvc/views/razor#key) pomocí atributu direktivy.</span><span class="sxs-lookup"><span data-stu-id="a5731-248">The mapping process can be controlled with the [`@key`](xref:mvc/views/razor#key) directive attribute.</span></span> <span data-ttu-id="a5731-249">`@key`způsobí, že algoritmus diffing zaručit zachování prvků nebo součástí na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="a5731-249">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="a5731-250">Při `People` změně kolekce algoritmus diffing zachová `<DetailsEditor>` přidružení `person` mezi instancemi a instancemi:</span><span class="sxs-lookup"><span data-stu-id="a5731-250">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="a5731-251">Pokud `Person` je odstraněn `People` ze seznamu, `<DetailsEditor>` pouze odpovídající instance je odebrána z ui.</span><span class="sxs-lookup"><span data-stu-id="a5731-251">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="a5731-252">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="a5731-252">Other instances are left unchanged.</span></span>
* <span data-ttu-id="a5731-253">Pokud `Person` je a vložen na některé místo `<DetailsEditor>` v seznamu, jedna nová instance je vložena na odpovídající pozici.</span><span class="sxs-lookup"><span data-stu-id="a5731-253">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="a5731-254">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="a5731-254">Other instances are left unchanged.</span></span>
* <span data-ttu-id="a5731-255">Pokud `Person` jsou položky znovu `<DetailsEditor>` objednány, odpovídající instance jsou zachovány a znovu uspořádány v ui.</span><span class="sxs-lookup"><span data-stu-id="a5731-255">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="a5731-256">V některých `@key` scénářích použití minimalizuje složitost rerendering a zabraňuje potenciální problémy se změnou stavové části DOM, jako je například umístění fokusu.</span><span class="sxs-lookup"><span data-stu-id="a5731-256">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a5731-257">Klíče jsou místní pro každý prvek kontejneru nebo součást.</span><span class="sxs-lookup"><span data-stu-id="a5731-257">Keys are local to each container element or component.</span></span> <span data-ttu-id="a5731-258">Klíče nejsou v celém dokumentu porovnávány globálně.</span><span class="sxs-lookup"><span data-stu-id="a5731-258">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="a5731-259">Kdy použít \@klíč</span><span class="sxs-lookup"><span data-stu-id="a5731-259">When to use \@key</span></span>

<span data-ttu-id="a5731-260">Obvykle má smysl použít `@key` vždy, když je vykreslen seznam (například v `@foreach` bloku) a existuje `@key`vhodná hodnota pro definování .</span><span class="sxs-lookup"><span data-stu-id="a5731-260">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="a5731-261">Můžete také `@key` zabránit Blazor zachování prvku nebo podstromu součásti při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="a5731-261">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="a5731-262">Pokud `@currentPerson` se `@key` změní, Blazor direktiva atributů vynutí zahodit celý `<div>` a jeho potomky a znovu vytvořit podstrom v rámci uživatelského rozhraní s novými prvky a součásti.</span><span class="sxs-lookup"><span data-stu-id="a5731-262">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="a5731-263">To může být užitečné, pokud potřebujete zaručit, že `@currentPerson` žádný stav ui je zachována při změnách.</span><span class="sxs-lookup"><span data-stu-id="a5731-263">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="a5731-264">Kdy nepoužívat \@klíč</span><span class="sxs-lookup"><span data-stu-id="a5731-264">When not to use \@key</span></span>

<span data-ttu-id="a5731-265">Při rozptylování s `@key`.</span><span class="sxs-lookup"><span data-stu-id="a5731-265">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="a5731-266">Náklady na výkon není velký, `@key` ale pouze určit, pokud řízení elementu nebo pravidla zachování komponenty prospěch aplikace.</span><span class="sxs-lookup"><span data-stu-id="a5731-266">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="a5731-267">I `@key` v případě, Blazor že se nepoužívá, zachová podřízené instance elementu a komponenty co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="a5731-267">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="a5731-268">Jedinou výhodou `@key` použití je kontrola nad *tím, jak* jsou instance modelu mapovány na instance zachované součásti, namísto algoritmu diffing, který vybírá mapování.</span><span class="sxs-lookup"><span data-stu-id="a5731-268">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="a5731-269">Jaké hodnoty použít \@pro klíč</span><span class="sxs-lookup"><span data-stu-id="a5731-269">What values to use for \@key</span></span>

<span data-ttu-id="a5731-270">Obecně má smysl dodávat jeden z následujících `@key`druhů hodnoty pro :</span><span class="sxs-lookup"><span data-stu-id="a5731-270">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="a5731-271">Instance objektu modelu (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="a5731-271">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="a5731-272">Tím je zajištěno zachování na základě rovnosti odkazů na objekt.</span><span class="sxs-lookup"><span data-stu-id="a5731-272">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="a5731-273">Jedinečné identifikátory (například hodnoty primárního `string`klíče `Guid`typu `int`, nebo ).</span><span class="sxs-lookup"><span data-stu-id="a5731-273">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="a5731-274">Ujistěte se, `@key` že hodnoty použité pro nekolidovat.</span><span class="sxs-lookup"><span data-stu-id="a5731-274">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="a5731-275">Pokud jsou zjištěny kolidující hodnoty Blazor v rámci stejného nadřazeného prvku, vyvolá výjimku, protože nemůže deterministicky mapovat staré prvky nebo součásti na nové prvky nebo součásti.</span><span class="sxs-lookup"><span data-stu-id="a5731-275">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="a5731-276">Používejte pouze odlišné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="a5731-276">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="a5731-277">Nevytvářejte součásti, které zapisují do vlastních vlastností parametrů</span><span class="sxs-lookup"><span data-stu-id="a5731-277">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="a5731-278">Parametry jsou přepsány za následujících podmínek:</span><span class="sxs-lookup"><span data-stu-id="a5731-278">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="a5731-279">Podřízený prvek obsah je vykreslen `RenderFragment`s .</span><span class="sxs-lookup"><span data-stu-id="a5731-279">A child component's content is rendered with a `RenderFragment`.</span></span>
* <span data-ttu-id="a5731-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>se nazývá v nadřazené součásti.</span><span class="sxs-lookup"><span data-stu-id="a5731-280"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="a5731-281">Parametry jsou resetovány, protože nadřazená komponenta se znovu vykresluje, když <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> je volána, a do podřízené součásti jsou dodávány nové hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="a5731-281">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="a5731-282">Zvažte `Expander` následující součást, která:</span><span class="sxs-lookup"><span data-stu-id="a5731-282">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="a5731-283">Vykreslí podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="a5731-283">Renders child content.</span></span>
* <span data-ttu-id="a5731-284">Přepíná zobrazení podřízeného obsahu s parametrem komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-284">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="a5731-285">Komponenta `Expander` je přidána do nadřazené součásti, která může volat `StateHasChanged`:</span><span class="sxs-lookup"><span data-stu-id="a5731-285">The `Expander` component is added to a parent component that may call `StateHasChanged`:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="a5731-286">Zpočátku `Expander` součásti chovat nezávisle, `Expanded` když jsou jejich vlastnosti přepnuto.</span><span class="sxs-lookup"><span data-stu-id="a5731-286">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="a5731-287">Podřízené součásti udržovat jejich stavy podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="a5731-287">The child components maintain their states as expected.</span></span> <span data-ttu-id="a5731-288">Když `StateHasChanged` je volána v `Expanded` nadřazeném, parametr první podřízené`true`součásti je obnoven zpět na počáteční hodnotu ( ).</span><span class="sxs-lookup"><span data-stu-id="a5731-288">When `StateHasChanged` is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="a5731-289">`Expanded` Hodnota druhé `Expander` součásti není resetována, protože v druhé součásti není vykreslen žádný podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="a5731-289">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="a5731-290">Chcete-li zachovat stav v předchozím scénáři, `Expander` použijte soukromé *pole* v komponentě k zachování jeho přeplovaný stav.</span><span class="sxs-lookup"><span data-stu-id="a5731-290">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="a5731-291">Následující `Expander` součást:</span><span class="sxs-lookup"><span data-stu-id="a5731-291">The following `Expander` component:</span></span>

* <span data-ttu-id="a5731-292">Přijme hodnotu parametru `Expanded` komponenty z nadřazeného objektu.</span><span class="sxs-lookup"><span data-stu-id="a5731-292">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="a5731-293">Přiřadí hodnotu parametru komponenty`_expanded` *soukromému poli* ( ) v události [OnInitialized](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="a5731-293">Assigns the component parameter value to a *private field* (`_expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="a5731-294">Používá soukromé pole k zachování jeho vnitřní přepnout stav.</span><span class="sxs-lookup"><span data-stu-id="a5731-294">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @_expanded)

    @if (_expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool _expanded;

    protected override void OnInitialized()
    {
        _expanded = Expanded;
    }

    private void Toggle()
    {
        _expanded = !_expanded;
    }
}
```

## <a name="partial-class-support"></a><span data-ttu-id="a5731-295">Podpora částečné třídy</span><span class="sxs-lookup"><span data-stu-id="a5731-295">Partial class support</span></span>

<span data-ttu-id="a5731-296">Komponenty razor jsou generovány jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="a5731-296">Razor components are generated as partial classes.</span></span> <span data-ttu-id="a5731-297">Komponenty břitvy jsou vytvářeny jedním z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a5731-297">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="a5731-298">Kód C# je [`@code`](xref:mvc/views/razor#code) definován v bloku se značkami HTML a kódem Razor v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="a5731-298">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="a5731-299">šablony definují své komponenty Razor pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="a5731-299"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="a5731-300">Kód Jazyka C# je umístěn v souboru s kódem na pozadí definovaném jako částečná třída.</span><span class="sxs-lookup"><span data-stu-id="a5731-300">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="a5731-301">Následující příklad ukazuje `Counter` výchozí komponentu s blokem `@code` v Blazor aplikaci vygenerované ze šablony.</span><span class="sxs-lookup"><span data-stu-id="a5731-301">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="a5731-302">Značky HTML, kód Razor a kód Jazyka C# jsou ve stejném souboru:</span><span class="sxs-lookup"><span data-stu-id="a5731-302">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="a5731-303">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5731-303">*Counter.razor*:</span></span>

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

<span data-ttu-id="a5731-304">Komponentu `Counter` lze také vytvořit pomocí souboru s kódem na pozadí s částečnou třídou:</span><span class="sxs-lookup"><span data-stu-id="a5731-304">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="a5731-305">*Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a5731-305">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="a5731-306">*Counter.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5731-306">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="a5731-307">Podle potřeby přidejte všechny požadované obory názvů do souboru částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="a5731-307">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="a5731-308">Mezi typické obory názvů používané komponentami Razor patří:</span><span class="sxs-lookup"><span data-stu-id="a5731-308">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="a5731-309">Určení základní třídy</span><span class="sxs-lookup"><span data-stu-id="a5731-309">Specify a base class</span></span>

<span data-ttu-id="a5731-310">Direktivu [`@inherits`](xref:mvc/views/razor#inherits) lze použít k určení základní třídy pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="a5731-310">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="a5731-311">Následující příklad ukazuje, jak může komponenta `BlazorRocksBase`zdědit základní třídu , a poskytnout tak vlastnosti a metody komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-311">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="a5731-312">Základní třída by `ComponentBase`měla být odvozena z .</span><span class="sxs-lookup"><span data-stu-id="a5731-312">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="a5731-313">*Stránky/BlazorRocks.břitva*:</span><span class="sxs-lookup"><span data-stu-id="a5731-313">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="a5731-314">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5731-314">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="a5731-315">Určení atributu</span><span class="sxs-lookup"><span data-stu-id="a5731-315">Specify an attribute</span></span>

<span data-ttu-id="a5731-316">Atributy lze zadat v komponenty [`@attribute`](xref:mvc/views/razor#attribute) Razor se směrnicí.</span><span class="sxs-lookup"><span data-stu-id="a5731-316">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="a5731-317">Následující příklad použije `[Authorize]` atribut pro třídu komponenty:</span><span class="sxs-lookup"><span data-stu-id="a5731-317">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="a5731-318">Import komponent</span><span class="sxs-lookup"><span data-stu-id="a5731-318">Import components</span></span>

<span data-ttu-id="a5731-319">Obor názvů komponenty vytvořené holicím strojkem je založen na (v pořadí podle priority):</span><span class="sxs-lookup"><span data-stu-id="a5731-319">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="a5731-320">[`@namespace`](xref:mvc/views/razor#namespace)označení v souboru Razor (`@namespace BlazorSample.MyNamespace`*.razor*) ().</span><span class="sxs-lookup"><span data-stu-id="a5731-320">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="a5731-321">Projekt je `RootNamespace` v souboru`<RootNamespace>BlazorSample</RootNamespace>`projektu ( ).</span><span class="sxs-lookup"><span data-stu-id="a5731-321">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="a5731-322">Název projektu převzatý z názvu souboru projektu (*.csproj*) a cesta z kořenového adresáře projektu do komponenty.</span><span class="sxs-lookup"><span data-stu-id="a5731-322">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="a5731-323">Například rozhraní překládá *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj* `BlazorSample.Pages`) do oboru názvů .</span><span class="sxs-lookup"><span data-stu-id="a5731-323">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="a5731-324">Součásti postupujte c# název vazby pravidla.</span><span class="sxs-lookup"><span data-stu-id="a5731-324">Components follow C# name binding rules.</span></span> <span data-ttu-id="a5731-325">Pro `Index` součást v tomto příkladu jsou součásti v oboru všechny součásti:</span><span class="sxs-lookup"><span data-stu-id="a5731-325">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="a5731-326">Ve stejné složce *stránky*.</span><span class="sxs-lookup"><span data-stu-id="a5731-326">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="a5731-327">Součásti v kořenovém adresáři projektu, které explicitně neurčují jiný obor názvů.</span><span class="sxs-lookup"><span data-stu-id="a5731-327">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="a5731-328">Součásti definované v jiném oboru názvů jsou uvedeny do oboru pomocí [`@using`](xref:mvc/views/razor#using) Razor směrnice.</span><span class="sxs-lookup"><span data-stu-id="a5731-328">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="a5731-329">Pokud ve `NavMenu.razor`složce *BlazorSample/Shared/* existuje jiná komponenta , `Index.razor` lze komponentu použít pomocí následujícího `@using` příkazu:</span><span class="sxs-lookup"><span data-stu-id="a5731-329">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="a5731-330">Součásti lze také odkazovat pomocí jejich plně kvalifikované názvy, což nevyžaduje [`@using`](xref:mvc/views/razor#using) směrnice:</span><span class="sxs-lookup"><span data-stu-id="a5731-330">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="a5731-331">Kvalifikace `global::` není podporována.</span><span class="sxs-lookup"><span data-stu-id="a5731-331">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="a5731-332">Import součástí pomocí aliasovaných `using` příkazů `@using Foo = Bar`(například) není podporován.</span><span class="sxs-lookup"><span data-stu-id="a5731-332">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="a5731-333">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="a5731-333">Partially qualified names aren't supported.</span></span> <span data-ttu-id="a5731-334">Například přidávání `@using BlazorSample` a `NavMenu.razor` `<Shared.NavMenu></Shared.NavMenu>` odkazování s není podporováno.</span><span class="sxs-lookup"><span data-stu-id="a5731-334">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="a5731-335">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="a5731-335">Conditional HTML element attributes</span></span>

<span data-ttu-id="a5731-336">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="a5731-336">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="a5731-337">Pokud je `false` hodnota `null`nebo , atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="a5731-337">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="a5731-338">Pokud je `true`hodnota , atribut je vykreslen minimalizovat.</span><span class="sxs-lookup"><span data-stu-id="a5731-338">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="a5731-339">V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslen ve značkách prvku:</span><span class="sxs-lookup"><span data-stu-id="a5731-339">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="a5731-340">Pokud `IsCompleted` `true`je , zaškrtávací políčko je vykresleno takto:</span><span class="sxs-lookup"><span data-stu-id="a5731-340">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="a5731-341">Pokud `IsCompleted` `false`je , zaškrtávací políčko je vykresleno takto:</span><span class="sxs-lookup"><span data-stu-id="a5731-341">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="a5731-342">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="a5731-342">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="a5731-343">Některé atributy HTML, například [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nefungují správně, `bool`pokud je typ .NET .</span><span class="sxs-lookup"><span data-stu-id="a5731-343">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="a5731-344">V těchto případech `string` použijte typ `bool`namísto .</span><span class="sxs-lookup"><span data-stu-id="a5731-344">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="a5731-345">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="a5731-345">Raw HTML</span></span>

<span data-ttu-id="a5731-346">Řetězce jsou obvykle vykresleny pomocí textových uzlů DOM, což znamená, že všechny značky, které mohou obsahovat, jsou ignorovány a považovány za doslovný text.</span><span class="sxs-lookup"><span data-stu-id="a5731-346">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="a5731-347">Chcete-li vykreslit nezpracovaný `MarkupString` kód HTML, zalomte obsah HTML do hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a5731-347">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="a5731-348">Hodnota je analyzována jako HTML nebo SVG a vložena do do m.</span><span class="sxs-lookup"><span data-stu-id="a5731-348">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="a5731-349">Vykreslování raw HTML vytvořené z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a je třeba se mu vyhnout!</span><span class="sxs-lookup"><span data-stu-id="a5731-349">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="a5731-350">Následující příklad ukazuje `MarkupString` použití typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="a5731-350">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="a5731-351">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="a5731-351">Cascading values and parameters</span></span>

<span data-ttu-id="a5731-352">V některých případech je nepohodlné tok dat z nadřazené součásti do podmořské součásti pomocí [parametrů komponenty](#component-parameters), zejména pokud existuje několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="a5731-352">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="a5731-353">Kaskádové hodnoty a parametry tento problém řeší tím, že poskytují nadřazené součásti pohodlný způsob, jak poskytnout hodnotu všem jejím nadřízeným součástem.</span><span class="sxs-lookup"><span data-stu-id="a5731-353">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="a5731-354">Kaskádové hodnoty a parametry také poskytují přístup pro komponenty koordinovat.</span><span class="sxs-lookup"><span data-stu-id="a5731-354">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="a5731-355">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="a5731-355">Theme example</span></span>

<span data-ttu-id="a5731-356">V následujícím příkladu z ukázkové aplikace `ThemeInfo` třída určuje informace o motivu, které mají stékat dolů hierarchií komponent, takže všechna tlačítka v dané části aplikace sdílejí stejný styl.</span><span class="sxs-lookup"><span data-stu-id="a5731-356">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="a5731-357">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5731-357">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="a5731-358">Nadřazená komponenta může poskytnout kaskádovou hodnotu pomocí komponenty Kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="a5731-358">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="a5731-359">Komponenta `CascadingValue` obtéká podstrom hierarchie komponent a dodává jednu hodnotu všem součástem v rámci tohoto podstromu.</span><span class="sxs-lookup"><span data-stu-id="a5731-359">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="a5731-360">Ukázková aplikace například určuje informace`ThemeInfo`o motivu ( ) v jednom z rozložení aplikace jako kaskádový parametr pro `@Body` všechny součásti, které tvoří tělo rozložení vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a5731-360">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="a5731-361">`ButtonClass`je přiřazena `btn-success` hodnota v součásti rozvržení.</span><span class="sxs-lookup"><span data-stu-id="a5731-361">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="a5731-362">Všechny potomkové součásti můžete `ThemeInfo` spotřebovat tuto vlastnost prostřednictvím kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="a5731-362">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="a5731-363">`CascadingValuesParametersLayout`Komponenty:</span><span class="sxs-lookup"><span data-stu-id="a5731-363">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="a5731-364">Chcete-li použít kaskádové hodnoty, komponenty deklarují kaskádové parametry pomocí atributu. `[CascadingParameter]`</span><span class="sxs-lookup"><span data-stu-id="a5731-364">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="a5731-365">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="a5731-365">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="a5731-366">V ukázkové aplikaci `CascadingValuesParametersTheme` komponenta `ThemeInfo` váže kaskádovou hodnotu na kaskádový parametr.</span><span class="sxs-lookup"><span data-stu-id="a5731-366">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="a5731-367">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="a5731-367">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="a5731-368">`CascadingValuesParametersTheme`Komponenty:</span><span class="sxs-lookup"><span data-stu-id="a5731-368">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="a5731-369">Chcete-li kaskádovat více hodnot stejného typu v `Name` rámci `CascadingValue` stejného podstromu, zadejte jedinečný řetězec pro každou komponentu a její odpovídající `CascadingParameter`.</span><span class="sxs-lookup"><span data-stu-id="a5731-369">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="a5731-370">V následujícím příkladu `CascadingValue` dvě součásti kaskádové různé instance `MyCascadingType` podle názvu:</span><span class="sxs-lookup"><span data-stu-id="a5731-370">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="a5731-371">V součásti potomku obdrží kaskádové parametry své hodnoty z odpovídajících kaskádových hodnot v součásti předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="a5731-371">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="a5731-372">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="a5731-372">TabSet example</span></span>

<span data-ttu-id="a5731-373">Kaskádové parametry také umožňují součástem spolupracovat v hierarchii komponent.</span><span class="sxs-lookup"><span data-stu-id="a5731-373">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="a5731-374">Zvažte například následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a5731-374">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="a5731-375">Ukázková aplikace `ITab` má rozhraní, které karty implementují:</span><span class="sxs-lookup"><span data-stu-id="a5731-375">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="a5731-376">Komponenta `CascadingValuesParametersTabSet` používá `TabSet` komponentu, `Tab` která obsahuje několik součástí:</span><span class="sxs-lookup"><span data-stu-id="a5731-376">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="a5731-377">Podřízené `Tab` součásti nejsou explicitně předány `TabSet`jako parametry .</span><span class="sxs-lookup"><span data-stu-id="a5731-377">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="a5731-378">Místo toho `Tab` podřízené součásti jsou součástí `TabSet`podřízeného obsahu .</span><span class="sxs-lookup"><span data-stu-id="a5731-378">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="a5731-379">Stále však `TabSet` potřebuje vědět `Tab` o každé součásti tak, aby mohla vykreslit záhlaví a aktivní kartu. Chcete-li povolit tuto koordinaci `TabSet` bez nutnosti dalšího kódu, *komponenta může poskytnout sám sebe jako kaskádové hodnoty,* která je pak zvedl podspodnou `Tab` součásti.</span><span class="sxs-lookup"><span data-stu-id="a5731-379">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="a5731-380">`TabSet`Komponenty:</span><span class="sxs-lookup"><span data-stu-id="a5731-380">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="a5731-381">Podřízené `Tab` součásti zachycují obsahující `TabSet` jako kaskádový parametr, `Tab` takže se komponenty `TabSet` přidají k souřadnici a souřadnice, na které je karta aktivní.</span><span class="sxs-lookup"><span data-stu-id="a5731-381">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="a5731-382">`Tab`Komponenty:</span><span class="sxs-lookup"><span data-stu-id="a5731-382">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="a5731-383">Šablony holicích strojků</span><span class="sxs-lookup"><span data-stu-id="a5731-383">Razor templates</span></span>

<span data-ttu-id="a5731-384">Render fragmenty lze definovat pomocí syntaxe šablony Razor.</span><span class="sxs-lookup"><span data-stu-id="a5731-384">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="a5731-385">Šablony holicího strojku představují způsob, jak definovat fragment uživatelského uživatelského nastavení a převzít následující formát:</span><span class="sxs-lookup"><span data-stu-id="a5731-385">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="a5731-386">Následující příklad ukazuje, jak `RenderFragment` `RenderFragment<T>` určit a hodnoty a vykreslení šablon přímo v komponentě.</span><span class="sxs-lookup"><span data-stu-id="a5731-386">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="a5731-387">Fragmenty vykreslení lze také předat jako argumenty [šablonované součásti](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="a5731-387">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="a5731-388">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="a5731-388">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="a5731-389">Škálovatelné obrazy vektorové grafiky (SVG)</span><span class="sxs-lookup"><span data-stu-id="a5731-389">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="a5731-390">Vzhledem k tomu, Blazor rendrování HTML, prohlížeč-podporované obrázky, včetně škálovatelné vektorové grafiky (SVG) obrázky (*.svg*), jsou podporovány prostřednictvím tag: `<img>`</span><span class="sxs-lookup"><span data-stu-id="a5731-390">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="a5731-391">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru stylů (*.css*):</span><span class="sxs-lookup"><span data-stu-id="a5731-391">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="a5731-392">Vložená značka SVG však není podporována ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="a5731-392">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="a5731-393">Pokud umístíte `<svg>` značku přímo do souboru komponenty *(.razor),* základní vykreslování obrázků je podporováno, ale mnoho pokročilých scénářů ještě není podporováno.</span><span class="sxs-lookup"><span data-stu-id="a5731-393">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="a5731-394">`<use>` Značky například nejsou v současné době `@bind` respektovány a nelze je použít u některých značek SVG.</span><span class="sxs-lookup"><span data-stu-id="a5731-394">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="a5731-395">Očekáváme, že tato omezení budeme řešit v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="a5731-395">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5731-396">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a5731-396">Additional resources</span></span>

* <span data-ttu-id="a5731-397"><xref:security/blazor/server>&ndash; Obsahuje pokyny Blazor pro vytváření serverových aplikací, které se musí potýkat s vyčerpáním prostředků.</span><span class="sxs-lookup"><span data-stu-id="a5731-397"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
