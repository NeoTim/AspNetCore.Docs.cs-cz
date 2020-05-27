---
<span data-ttu-id="de451-101">title: ' Vytvoření a použití ASP.NET Core Razor komponenty ' Author: Description: ' Naučte se vytvářet a používat Razor komponenty, včetně toho, jak navazovat data, zpracovávat události a spravovat životní cykly komponent. '</span><span class="sxs-lookup"><span data-stu-id="de451-101">title: 'Create and use ASP.NET Core Razor components' author: description: 'Learn how to create and use Razor components, including how to bind to data, handle events, and manage component life cycles.'</span></span>
<span data-ttu-id="de451-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="de451-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="de451-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="de451-103">'Blazor'</span></span>
- <span data-ttu-id="de451-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="de451-104">'Identity'</span></span>
- <span data-ttu-id="de451-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="de451-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="de451-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="de451-106">'Razor'</span></span>
- <span data-ttu-id="de451-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="de451-107">'SignalR' uid:</span></span> 

---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="de451-108">Vytvoření a použití Razor komponent ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de451-108">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="de451-109">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="de451-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="de451-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de451-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="de451-111">aplikace jsou sestavené pomocí *komponent*.</span><span class="sxs-lookup"><span data-stu-id="de451-111"> apps are built using *components*.</span></span> <span data-ttu-id="de451-112">Součást je samostatně obsažený blok uživatelského rozhraní (UI), jako je například stránka, dialogové okno nebo formulář.</span><span class="sxs-lookup"><span data-stu-id="de451-112">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="de451-113">Komponenta obsahuje značky HTML a logiku zpracování potřebnou k vkládání dat nebo reakci na události uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="de451-113">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="de451-114">Komponenty jsou flexibilní a odlehčené.</span><span class="sxs-lookup"><span data-stu-id="de451-114">Components are flexible and lightweight.</span></span> <span data-ttu-id="de451-115">Můžou být vnořené, opakovaně používané a sdílené mezi projekty.</span><span class="sxs-lookup"><span data-stu-id="de451-115">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="de451-116">Třídy komponent</span><span class="sxs-lookup"><span data-stu-id="de451-116">Component classes</span></span>

<span data-ttu-id="de451-117">Komponenty jsou implementovány v [Razor](xref:mvc/views/razor) souborech součástí (*. Razor*) pomocí kombinace kódu jazyka C# a HTML.</span><span class="sxs-lookup"><span data-stu-id="de451-117">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="de451-118">Komponenta v Blazor je formálně označována jako \* Razor Komponenta\*.</span><span class="sxs-lookup"><span data-stu-id="de451-118">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="de451-119">Název součásti musí začínat velkým znakem.</span><span class="sxs-lookup"><span data-stu-id="de451-119">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="de451-120">Například *MyCoolComponent. Razor* je platný a *MyCoolComponent. Razor* je neplatný.</span><span class="sxs-lookup"><span data-stu-id="de451-120">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="de451-121">Uživatelské rozhraní pro komponentu je definováno pomocí jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="de451-121">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="de451-122">Dynamická logika vykreslování (například cykly, podmíněné výrazy, výrazy) je přidána pomocí vložené syntaxe jazyka C# s názvem *Razor* .</span><span class="sxs-lookup"><span data-stu-id="de451-122">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="de451-123">Při kompilaci aplikace jsou značky HTML a vykreslování jazyka C# převedeny na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="de451-123">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="de451-124">Název generované třídy se shoduje s názvem souboru.</span><span class="sxs-lookup"><span data-stu-id="de451-124">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="de451-125">Členy třídy komponenty jsou definovány v [`@code`][1] bloku.</span><span class="sxs-lookup"><span data-stu-id="de451-125">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="de451-126">V [`@code`][1] bloku je stav součásti (vlastnosti, pole) zadán pomocí metod pro zpracování událostí nebo pro definování jiné logiky komponent.</span><span class="sxs-lookup"><span data-stu-id="de451-126">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="de451-127">Je přípustný více než jeden [`@code`][1] blok.</span><span class="sxs-lookup"><span data-stu-id="de451-127">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="de451-128">Členy součásti lze použít jako součást logiky vykreslování komponenty pomocí výrazů jazyka C#, které začínají na `@` .</span><span class="sxs-lookup"><span data-stu-id="de451-128">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="de451-129">Například pole C# se vykreslí pomocí předpony `@` na název pole.</span><span class="sxs-lookup"><span data-stu-id="de451-129">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="de451-130">Následující příklad vyhodnocuje a vykresluje:</span><span class="sxs-lookup"><span data-stu-id="de451-130">The following example evaluates and renders:</span></span>

* <span data-ttu-id="de451-131">`headingFontStyle`do hodnoty vlastnosti CSS pro `font-style` .</span><span class="sxs-lookup"><span data-stu-id="de451-131">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="de451-132">`headingText`k obsahu `<h1>` elementu.</span><span class="sxs-lookup"><span data-stu-id="de451-132">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="de451-133">Po prvním vykreslení komponenty vygeneruje komponenta znovu svůj strom vykreslování v reakci na události.</span><span class="sxs-lookup"><span data-stu-id="de451-133">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> Blazor<span data-ttu-id="de451-134">pak porovná nový strom vykreslování s předchozí a aplikuje všechny úpravy model DOM (Document Object Model) v prohlížeči (DOM).</span><span class="sxs-lookup"><span data-stu-id="de451-134"> then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="de451-135">Komponenty jsou běžné třídy jazyka C# a lze je umístit kamkoli v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="de451-135">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="de451-136">Komponenty, které tvoří webové stránky, jsou obvykle umístěny ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="de451-136">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="de451-137">Komponenty mimo stránku jsou často umístěny ve *sdílené* složce nebo vlastní složce přidané do projektu.</span><span class="sxs-lookup"><span data-stu-id="de451-137">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="de451-138">Obor názvů komponenty obvykle je odvozen z kořenového oboru názvů aplikace a umístění komponenty (složka) v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="de451-138">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="de451-139">Pokud je kořenový obor názvů aplikace `BlazorApp` a součást se nachází `Counter` ve složce *stránky* :</span><span class="sxs-lookup"><span data-stu-id="de451-139">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="de451-140">`Counter`Obor názvů součásti je `BlazorApp.Pages` .</span><span class="sxs-lookup"><span data-stu-id="de451-140">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="de451-141">Plně kvalifikovaný název typu komponenty je `BlazorApp.Pages.Counter` .</span><span class="sxs-lookup"><span data-stu-id="de451-141">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="de451-142">Pro vlastní složky, které obsahují součásti, přidejte `using` příkaz do nadřazené komponenty nebo do souboru *_Imports. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="de451-142">For custom folders that hold components, add a `using` statement to the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="de451-143">Následující příklad zpřístupňuje komponenty ve složce *Components* :</span><span class="sxs-lookup"><span data-stu-id="de451-143">The following example makes components in the *Components* folder available:</span></span>

```razor
@using BlazorApp.Components
```

<span data-ttu-id="de451-144">Alternativně může být komponenta přímo odkazována:</span><span class="sxs-lookup"><span data-stu-id="de451-144">Alternatively, a component can be directly referenced:</span></span>

```razor
<BlazorApp.Components.MyCoolComponent />
```

<span data-ttu-id="de451-145">Další informace naleznete v části [Import komponent](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="de451-145">For more information, see the [Import components](#import-components) section.</span></span>

## <a name="razor-syntax"></a>Razor<span data-ttu-id="de451-146">syntaktick</span><span class="sxs-lookup"><span data-stu-id="de451-146"> syntax</span></span>

Razor<span data-ttu-id="de451-147">komponenty v Blazor aplikacích výrazně používají Razor syntaxi.</span><span class="sxs-lookup"><span data-stu-id="de451-147"> components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="de451-148">Pokud nejste obeznámeni s Razor jazykem označení, doporučujeme <xref:mvc/views/razor> před pokračováním číst.</span><span class="sxs-lookup"><span data-stu-id="de451-148">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="de451-149">Při přístupu k obsahu v Razor syntaxi věnujte zvláštní pozornost následujícím oddílům:</span><span class="sxs-lookup"><span data-stu-id="de451-149">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="de451-150">[Direktivy](xref:mvc/views/razor#directives) &ndash; `@`-pevná vyhrazená klíčová slova, která obvykle mění způsob, jakým jsou analyzovány značky komponenty nebo funkce.</span><span class="sxs-lookup"><span data-stu-id="de451-150">[Directives](xref:mvc/views/razor#directives) &ndash; `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="de451-151">[Atributy direktiv](xref:mvc/views/razor#directive-attributes) &ndash; `@`-pevná vyhrazená klíčová slova, která obvykle mění způsob, jakým jsou analyzovány prvky součásti nebo funkce.</span><span class="sxs-lookup"><span data-stu-id="de451-151">[Directive attributes](xref:mvc/views/razor#directive-attributes) &ndash; `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

## <a name="static-assets"></a><span data-ttu-id="de451-152">Statické prostředky</span><span class="sxs-lookup"><span data-stu-id="de451-152">Static assets</span></span>

Blazor<span data-ttu-id="de451-153">postupuje podle konvence ASP.NET Core aplikací, které umísťují statické prostředky do [složky webového kořenového adresáře (wwwroot)](xref:fundamentals/index#web-root)projektu.</span><span class="sxs-lookup"><span data-stu-id="de451-153"> follows the convention of ASP.NET Core apps placing static assets under the project's [web root (wwwroot) folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="de451-154">Použijte základní cestu ( `/` ) pro odkaz na webový kořenový adresář pro statický prostředek.</span><span class="sxs-lookup"><span data-stu-id="de451-154">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="de451-155">V následujícím příkladu je soubor *logo. png* fyzicky umístěný ve složce *{Project root}/wwwroot/images* :</span><span class="sxs-lookup"><span data-stu-id="de451-155">In the following example, *logo.png* is physically located in the *{PROJECT ROOT}/wwwroot/images* folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor<span data-ttu-id="de451-156">komponenty **nepodporují** vlnové lomítko ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="de451-156"> components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="de451-157">Informace o nastavení základní cesty aplikace najdete v tématu <xref:host-and-deploy/blazor/index#app-base-path> .</span><span class="sxs-lookup"><span data-stu-id="de451-157">For information on setting an app's base path, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="de451-158">V součástech nejsou podporovány pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="de451-158">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="de451-159">[Tag Helpers](xref:mvc/views/tag-helpers/intro) V Razor součástech (soubory *. Razor* ) nejsou podporované pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="de451-159">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="de451-160">Chcete-li poskytnout funkci, jako je například Pomocník pro Blazor vytváření značek, vytvořte komponentu se stejnou funkcí jako pomocník značek a místo ní použijte komponentu.</span><span class="sxs-lookup"><span data-stu-id="de451-160">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="de451-161">Použití komponent</span><span class="sxs-lookup"><span data-stu-id="de451-161">Use components</span></span>

<span data-ttu-id="de451-162">Komponenty mohou zahrnovat další komponenty deklarováním pomocí syntaxe elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="de451-162">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="de451-163">Označení pro použití komponenty vypadá jako značka HTML, kde název značky je typ komponenty.</span><span class="sxs-lookup"><span data-stu-id="de451-163">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="de451-164">Následující kód v *indexu. Razor* vykreslí `HeadingComponent` instanci:</span><span class="sxs-lookup"><span data-stu-id="de451-164">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="de451-165">*Components/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-165">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="de451-166">Pokud komponenta obsahuje element HTML s velkým prvním písmenem, které neodpovídá názvu komponenty, je vygenerováno upozornění označující, že element má neočekávaný název.</span><span class="sxs-lookup"><span data-stu-id="de451-166">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="de451-167">Přidání [`@using`][2] direktivy pro obor názvů součásti zpřístupňuje komponentu, která vyřeší upozornění.</span><span class="sxs-lookup"><span data-stu-id="de451-167">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="de451-168">Směrování</span><span class="sxs-lookup"><span data-stu-id="de451-168">Routing</span></span>

<span data-ttu-id="de451-169">Směrování do Blazor se dosahuje tím, že poskytuje šablonu směrování pro každou dostupnou součást aplikace.</span><span class="sxs-lookup"><span data-stu-id="de451-169">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="de451-170">Když Razor je zkompilován soubor s [`@page`][9] direktivou, vygenerovaná třída je dána <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> zadáním šablony trasy.</span><span class="sxs-lookup"><span data-stu-id="de451-170">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="de451-171">V době běhu Směrovač vyhledává třídy komponent pomocí <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> a a vykreslí, že jakákoli součást má šablonu směrování, která odpovídá požadované adrese URL.</span><span class="sxs-lookup"><span data-stu-id="de451-171">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="de451-172">Další informace naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="de451-172">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="de451-173">Parametry</span><span class="sxs-lookup"><span data-stu-id="de451-173">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="de451-174">Parametry směrování</span><span class="sxs-lookup"><span data-stu-id="de451-174">Route parameters</span></span>

<span data-ttu-id="de451-175">Komponenty mohou přijímat parametry směrování z šablony směrování uvedené v [`@page`][9] direktivě.</span><span class="sxs-lookup"><span data-stu-id="de451-175">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="de451-176">Směrovač používá parametry směrování k naplnění odpovídajících parametrů komponent.</span><span class="sxs-lookup"><span data-stu-id="de451-176">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="de451-177">*Stránky/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-177">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="de451-178">Volitelné parametry nejsou podporovány, takže [`@page`][9] v předchozím příkladu jsou aplikovány dvě direktivy.</span><span class="sxs-lookup"><span data-stu-id="de451-178">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="de451-179">První umožňuje navigaci na součást bez parametru.</span><span class="sxs-lookup"><span data-stu-id="de451-179">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="de451-180">Druhá [`@page`][9] direktiva přijme `{text}` parametr Route a přiřadí hodnotu `Text` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="de451-180">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="de451-181">*Catch-All* Parameter Syntax ( `*` / `**` ), která zachycuje cestu mezi více hranicemi složek, není **not** v Razor součástech (*. Razor*) podporován.</span><span class="sxs-lookup"><span data-stu-id="de451-181">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="de451-182">Parametry součásti</span><span class="sxs-lookup"><span data-stu-id="de451-182">Component parameters</span></span>

<span data-ttu-id="de451-183">Komponenty mohou mít *parametry komponenty*, které jsou definovány pomocí veřejných vlastností třídy Component s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributem] (odkazy XREF: Microsoft. AspNetCore. Components. ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="de451-183">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="de451-184">Použijte atributy k určení argumentů pro komponentu v kódu.</span><span class="sxs-lookup"><span data-stu-id="de451-184">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="de451-185">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-185">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="de451-186">V následujícím příkladu z ukázkové aplikace `ParentComponent` nastaví hodnotu `Title` vlastnosti `ChildComponent` .</span><span class="sxs-lookup"><span data-stu-id="de451-186">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="de451-187">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-187">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="de451-188">Nevytvářejte komponenty, které zapisují do vlastních *parametrů komponenty*, místo toho použijte soukromé pole.</span><span class="sxs-lookup"><span data-stu-id="de451-188">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="de451-189">Další informace naleznete v části [Nevytvářet komponenty, které jsou zapsány do jejich vlastních vlastností parametrů](#dont-create-components-that-write-to-their-own-parameter-properties) .</span><span class="sxs-lookup"><span data-stu-id="de451-189">For more information, see the [Don't create components that write to their own parameter properties](#dont-create-components-that-write-to-their-own-parameter-properties) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="de451-190">Podřízený obsah</span><span class="sxs-lookup"><span data-stu-id="de451-190">Child content</span></span>

<span data-ttu-id="de451-191">Komponenty mohou nastavit obsah jiné součásti.</span><span class="sxs-lookup"><span data-stu-id="de451-191">Components can set the content of another component.</span></span> <span data-ttu-id="de451-192">Součást přiřazení poskytuje obsah mezi značkami, které určují přijímací komponentu.</span><span class="sxs-lookup"><span data-stu-id="de451-192">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="de451-193">V následujícím příkladu `ChildComponent` má `ChildContent` vlastnost, která představuje, který představuje <xref:Microsoft.AspNetCore.Components.RenderFragment> segment uživatelského rozhraní pro vykreslení.</span><span class="sxs-lookup"><span data-stu-id="de451-193">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="de451-194">Hodnota `ChildContent` je umístěna v označení komponenty, kde má být obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="de451-194">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="de451-195">Hodnota `ChildContent` je přijímána z nadřazené komponenty a vykreslena v panelu Bootstrap `panel-body` .</span><span class="sxs-lookup"><span data-stu-id="de451-195">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="de451-196">*Components/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-196">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="de451-197">Vlastnost, která přijímá <xref:Microsoft.AspNetCore.Components.RenderFragment> obsah, musí být pojmenována `ChildContent` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="de451-197">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="de451-198">`ParentComponent`V ukázkové aplikaci může poskytovat obsah pro vykreslování `ChildComponent` umístěním obsahu uvnitř `<ChildComponent>` značek.</span><span class="sxs-lookup"><span data-stu-id="de451-198">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="de451-199">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-199">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="de451-200">Seskupováním atributů a libovolné parametry</span><span class="sxs-lookup"><span data-stu-id="de451-200">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="de451-201">Komponenty mohou kromě deklarovaných parametrů komponenty zachytit a vykreslovat další atributy.</span><span class="sxs-lookup"><span data-stu-id="de451-201">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="de451-202">Další atributy mohou být zachyceny ve slovníku a poté *splatted* na prvek při vykreslení komponenty pomocí [`@attributes`][3] Razor direktivy.</span><span class="sxs-lookup"><span data-stu-id="de451-202">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="de451-203">Tento scénář je užitečný při definování komponenty, která vytváří prvek značky, který podporuje nejrůznější přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="de451-203">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="de451-204">Například může být zdlouhavé definovat atributy samostatně pro objekt `<input>` , který podporuje mnoho parametrů.</span><span class="sxs-lookup"><span data-stu-id="de451-204">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="de451-205">V následujícím příkladu první `<input>` prvek ( `id="useIndividualParams"` ) používá jednotlivé parametry komponenty, zatímco druhý `<input>` element ( `id="useAttributesDict"` ) používá atribut seskupováním:</span><span class="sxs-lookup"><span data-stu-id="de451-205">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="de451-206">Typ parametru musí být implementován `IEnumerable<KeyValuePair<string, object>>` pomocí řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="de451-206">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="de451-207">Použití `IReadOnlyDictionary<string, object>` je také možností v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="de451-207">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="de451-208">Vykreslené `<input>` elementy pomocí obou přístupů jsou identické:</span><span class="sxs-lookup"><span data-stu-id="de451-208">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="de451-209">Chcete-li přijmout libovolné atributy, definujte parametr komponenty pomocí [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) atributu s <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> vlastností nastavenou na `true` :</span><span class="sxs-lookup"><span data-stu-id="de451-209">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="de451-210"><xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>Vlastnost on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) umožňuje, aby parametr odpovídal všem atributům, které se neshodují s žádným jiným parametrem.</span><span class="sxs-lookup"><span data-stu-id="de451-210">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="de451-211">Komponenta může definovat pouze jeden parametr s <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> .</span><span class="sxs-lookup"><span data-stu-id="de451-211">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="de451-212">Typ vlastnosti používaný pomocí <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> musí být přiřazovatelné z `Dictionary<string, object>` řetězcových klíčů.</span><span class="sxs-lookup"><span data-stu-id="de451-212">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="de451-213">`IEnumerable<KeyValuePair<string, object>>`nebo `IReadOnlyDictionary<string, object>` jsou také možnosti v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="de451-213">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="de451-214">Pozice [`@attributes`][3] relativní vzhledem k poloze atributů elementu je důležitá.</span><span class="sxs-lookup"><span data-stu-id="de451-214">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="de451-215">Když [`@attributes`][3] jsou splatted na elementu, atributy jsou zpracovávány zprava doleva (poslední až první).</span><span class="sxs-lookup"><span data-stu-id="de451-215">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="de451-216">Vezměte v úvahu následující příklad komponenty, která využívá `Child` komponentu:</span><span class="sxs-lookup"><span data-stu-id="de451-216">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="de451-217">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-217">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="de451-218">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-218">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="de451-219">`Child` `extra` Atribut komponenty je nastaven na hodnotu napravo od [`@attributes`][3] .</span><span class="sxs-lookup"><span data-stu-id="de451-219">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="de451-220">`Parent`Vykreslená komponenta `<div>` obsahuje `extra="5"` při předání prostřednictvím dodatečného atributu, protože atributy jsou zpracovávány zprava doleva (poslední až první):</span><span class="sxs-lookup"><span data-stu-id="de451-220">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="de451-221">V následujícím příkladu `extra` je pořadí a [`@attributes`][3] obrácené v `Child` komponentě `<div>` :</span><span class="sxs-lookup"><span data-stu-id="de451-221">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="de451-222">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-222">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="de451-223">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-223">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="de451-224">Vykreslená `<div>` `Parent` Komponenta obsahuje, `extra="10"` když je předána pomocí dalšího atributu:</span><span class="sxs-lookup"><span data-stu-id="de451-224">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="de451-225">Zachytit odkazy na komponenty</span><span class="sxs-lookup"><span data-stu-id="de451-225">Capture references to components</span></span>

<span data-ttu-id="de451-226">Odkazy na komponenty poskytují způsob, jak odkazovat na instanci komponenty, abyste mohli vydávat příkazy do této instance, například `Show` nebo `Reset` .</span><span class="sxs-lookup"><span data-stu-id="de451-226">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="de451-227">Zachytit odkaz na komponentu:</span><span class="sxs-lookup"><span data-stu-id="de451-227">To capture a component reference:</span></span>

* <span data-ttu-id="de451-228">Přidejte [`@ref`][4] atribut do podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="de451-228">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="de451-229">Definujte pole stejného typu jako podřízená komponenta.</span><span class="sxs-lookup"><span data-stu-id="de451-229">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="de451-230">Při vykreslení komponenty `loginDialog` je pole vyplněno `MyLoginDialog` instancí podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="de451-230">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="de451-231">Pak můžete vyvolat metody .NET v instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="de451-231">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="de451-232">`loginDialog`Proměnná je naplněna pouze po vykreslení komponenty a její výstup obsahuje `MyLoginDialog` element.</span><span class="sxs-lookup"><span data-stu-id="de451-232">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="de451-233">Do tohoto okamžiku neexistuje žádný odkaz na.</span><span class="sxs-lookup"><span data-stu-id="de451-233">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="de451-234">Chcete-li manipulovat s odkazy na součásti po dokončení vykreslování komponenty, použijte [metody OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="de451-234">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="de451-235">Chcete-li odkazovat na součásti ve smyčce, viz [zachytit odkazy na více podobných podřízených komponent (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="de451-235">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="de451-236">Při zachytávání odkazů na součásti použijte podobnou syntaxi pro [zachycení odkazů na prvky](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), není to funkce interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="de451-236">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="de451-237">Odkazy na součásti nejsou předány kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="de451-237">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="de451-238">Odkazy na komponenty se používají pouze v kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="de451-238">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="de451-239">Nepoužívejte odkazy na součásti **pro použití stavu** podřízených komponent.</span><span class="sxs-lookup"><span data-stu-id="de451-239">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="de451-240">Místo toho použijte k předání dat podřízeným komponentám běžné deklarativní parametry.</span><span class="sxs-lookup"><span data-stu-id="de451-240">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="de451-241">Použití běžných deklarativních parametrů má za následek podřízené komponenty, které jsou automaticky revykreslovány ve správný čas.</span><span class="sxs-lookup"><span data-stu-id="de451-241">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="de451-242">Vyvolat metody komponenty externě na stav aktualizace</span><span class="sxs-lookup"><span data-stu-id="de451-242">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="de451-243">používá kontext synchronizace ( <xref:System.Threading.SynchronizationContext> ) k vykonání jediného logického vlákna provádění.</span><span class="sxs-lookup"><span data-stu-id="de451-243"> uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="de451-244">[Metody životního cyklu](xref:blazor/lifecycle) komponenty a všechna zpětná volání událostí, která jsou vyvolána, Blazor jsou spouštěna v kontextu synchronizace.</span><span class="sxs-lookup"><span data-stu-id="de451-244">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

Blazor<span data-ttu-id="de451-245">Kontext synchronizace serveru se pokouší emulovat prostředí s jedním vláknem tak, aby přesně odpovídal modelu webového sestavení v prohlížeči, který je jediným vláknem.</span><span class="sxs-lookup"><span data-stu-id="de451-245"> Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="de451-246">V jakémkoli daném časovém okamžiku se práce provádí v přesně jednom vlákně, což dává dojem o jednom logickém vlákně.</span><span class="sxs-lookup"><span data-stu-id="de451-246">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="de451-247">Žádné dvě operace se neprovádějí současně.</span><span class="sxs-lookup"><span data-stu-id="de451-247">No two operations execute concurrently.</span></span>

<span data-ttu-id="de451-248">V případě, že komponenta musí být aktualizována na základě externí události, jako je například časovač nebo jiné oznámení, použijte `InvokeAsync` metodu, která odesílá Blazor kontext synchronizace.</span><span class="sxs-lookup"><span data-stu-id="de451-248">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="de451-249">Představte si například *službu* pro upozorňování, která může oznámit všechny součásti, které jsou v aktualizovaném stavu:</span><span class="sxs-lookup"><span data-stu-id="de451-249">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="de451-250">Zaregistrujte `NotifierService` jako singletion:</span><span class="sxs-lookup"><span data-stu-id="de451-250">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="de451-251">V rámci Blazor služby WebAssembly Zaregistrujte službu v `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="de451-251">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="de451-252">V části Blazor Server Zaregistrujte službu v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="de451-252">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="de451-253">`NotifierService`K aktualizaci součásti použijte.</span><span class="sxs-lookup"><span data-stu-id="de451-253">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="de451-254">V předchozím příkladu `NotifierService` vyvolá `OnNotify` metodu komponenty mimo Blazor kontext synchronizace.</span><span class="sxs-lookup"><span data-stu-id="de451-254">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="de451-255">`InvokeAsync`slouží k přepnutí do správného kontextu a vykreslení vykreslování do fronty.</span><span class="sxs-lookup"><span data-stu-id="de451-255">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="de451-256">Použití \@ klíče k řízení uchovávání prvků a komponent</span><span class="sxs-lookup"><span data-stu-id="de451-256">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="de451-257">Při vykreslování seznamu prvků nebo komponent a následné změny prvků nebo komponent se Blazor musí rozhodnout, který z předchozích prvků nebo komponent může být zachován a jak se mají objekty modelu namapovat.</span><span class="sxs-lookup"><span data-stu-id="de451-257">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="de451-258">Obvykle je tento proces automatický a může být ignorován, ale existují případy, kdy můžete chtít řídit proces.</span><span class="sxs-lookup"><span data-stu-id="de451-258">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="de451-259">Uvažujte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="de451-259">Consider the following example:</span></span>

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

<span data-ttu-id="de451-260">Obsah `People` kolekce se může změnit vloženými, odstraněnými nebo znovu seřazenými položkami.</span><span class="sxs-lookup"><span data-stu-id="de451-260">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="de451-261">Když se komponenta znovu vykreslí, může se tato `<DetailsEditor>` součást změnit, aby přijímala jiné `Details` hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="de451-261">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="de451-262">To může způsobit složitější revykreslování, než se očekávalo.</span><span class="sxs-lookup"><span data-stu-id="de451-262">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="de451-263">V některých případech může reprodukce vést k viditelným rozdílům v chování, jako je například ztracený fokus elementu.</span><span class="sxs-lookup"><span data-stu-id="de451-263">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="de451-264">Proces mapování lze řídit pomocí [`@key`][5] atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="de451-264">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="de451-265">[`@key`][5]způsobí, že rozdílový algoritmus garantuje zachování prvků nebo komponent na základě hodnoty klíče:</span><span class="sxs-lookup"><span data-stu-id="de451-265">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="de451-266">Když se `People` kolekce změní, rozdílový algoritmus zachovává přidružení mezi `<DetailsEditor>` instancemi a `person` instancemi:</span><span class="sxs-lookup"><span data-stu-id="de451-266">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="de451-267">Pokud `Person` se ze `People` seznamu odstraní, `<DetailsEditor>` z uživatelského rozhraní se odebere jenom odpovídající instance.</span><span class="sxs-lookup"><span data-stu-id="de451-267">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="de451-268">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="de451-268">Other instances are left unchanged.</span></span>
* <span data-ttu-id="de451-269">Pokud `Person` je vložena na nějaké místo v seznamu, `<DetailsEditor>` je do příslušné pozice vložena jedna nová instance.</span><span class="sxs-lookup"><span data-stu-id="de451-269">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="de451-270">Ostatní instance zůstanou beze změny.</span><span class="sxs-lookup"><span data-stu-id="de451-270">Other instances are left unchanged.</span></span>
* <span data-ttu-id="de451-271">Pokud `Person` jsou položky znovu seřazeny, odpovídající `<DetailsEditor>` instance jsou zachovány a znovu uspořádány v uživatelském rozhraní.</span><span class="sxs-lookup"><span data-stu-id="de451-271">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="de451-272">V některých scénářích použití [`@key`][5] minimalizuje složitost reprodukce a vyhne se potenciálním problémům se stavovou částí modelu DOM, jako je například pozice fokusu.</span><span class="sxs-lookup"><span data-stu-id="de451-272">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="de451-273">Klíče jsou místní pro každý prvek kontejneru nebo komponentu.</span><span class="sxs-lookup"><span data-stu-id="de451-273">Keys are local to each container element or component.</span></span> <span data-ttu-id="de451-274">Klíče nejsou v dokumentu globálně porovnány.</span><span class="sxs-lookup"><span data-stu-id="de451-274">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="de451-275">Kdy použít \@ klíč</span><span class="sxs-lookup"><span data-stu-id="de451-275">When to use \@key</span></span>

<span data-ttu-id="de451-276">Obvykle má smysl použít [`@key`][5] při každém vykreslení seznamu (například v bloku [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) ) a existuje vhodná hodnota pro definování [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="de451-276">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="de451-277">Můžete také použít [`@key`][5] k zabránění Blazor v zachování prvku nebo podstromu komponenty při změně objektu:</span><span class="sxs-lookup"><span data-stu-id="de451-277">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="de451-278">Pokud se `@currentPerson` změní, [`@key`][5] direktiva Attribute vynutí Blazor zrušení celého `<div>` a jeho následníků a znovu sestaví podstrom v uživatelském rozhraní novými prvky a komponentami.</span><span class="sxs-lookup"><span data-stu-id="de451-278">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="de451-279">To může být užitečné, pokud potřebujete zaručit, že se při změnách nezachovají stav uživatelského rozhraní `@currentPerson` .</span><span class="sxs-lookup"><span data-stu-id="de451-279">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="de451-280">Kdy nepoužívat \@ klíč</span><span class="sxs-lookup"><span data-stu-id="de451-280">When not to use \@key</span></span>

<span data-ttu-id="de451-281">Při rozdílech s se účtují náklady na výkon [`@key`][5] .</span><span class="sxs-lookup"><span data-stu-id="de451-281">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="de451-282">Náklady na výkon nejsou velké, ale zadávejte jenom [`@key`][5] v případě, že řízení pravidel uchovávání prvků nebo součástí má aplikace výhodu.</span><span class="sxs-lookup"><span data-stu-id="de451-282">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="de451-283">I v případě [`@key`][5] , že není použit, Blazor zachová podřízený element a instance komponenty co nejvíce.</span><span class="sxs-lookup"><span data-stu-id="de451-283">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="de451-284">Jedinou výhodou použití [`@key`][5] je kontrola, *jak* jsou instance modelů mapovány na zachované instance komponent namísto rozdílového algoritmu výběru mapování.</span><span class="sxs-lookup"><span data-stu-id="de451-284">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="de451-285">Jaké hodnoty se mají použít pro \@ klíč</span><span class="sxs-lookup"><span data-stu-id="de451-285">What values to use for \@key</span></span>

<span data-ttu-id="de451-286">Obecně dává smysl pro zadání jednoho z následujících typů hodnot pro [`@key`][5] :</span><span class="sxs-lookup"><span data-stu-id="de451-286">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="de451-287">Instance objektů modelu (například `Person` instance jako v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="de451-287">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="de451-288">To zajišťuje zachování v závislosti na rovnosti odkazů na objekty.</span><span class="sxs-lookup"><span data-stu-id="de451-288">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="de451-289">Jedinečné identifikátory (například hodnoty primárního klíče typu `int` , `string` nebo `Guid` ).</span><span class="sxs-lookup"><span data-stu-id="de451-289">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="de451-290">Zajistěte, aby hodnoty použité pro [`@key`][5] nekolidovat.</span><span class="sxs-lookup"><span data-stu-id="de451-290">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="de451-291">Pokud jsou v rámci stejného nadřazeného prvku zjištěny hodnoty konfliktu, Blazor vyvolá výjimku, protože nemůže deterministické namapovat staré prvky nebo součásti na nové prvky nebo komponenty.</span><span class="sxs-lookup"><span data-stu-id="de451-291">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="de451-292">Používejte pouze jedinečné hodnoty, například instance objektů nebo hodnoty primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="de451-292">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a><span data-ttu-id="de451-293">Nevytvářejte komponenty, které zapisují do vlastností vlastního parametru.</span><span class="sxs-lookup"><span data-stu-id="de451-293">Don't create components that write to their own parameter properties</span></span>

<span data-ttu-id="de451-294">Parametry jsou přepsány za následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="de451-294">Parameters are overwritten under the following conditions:</span></span>

* <span data-ttu-id="de451-295">Obsah podřízené komponenty je vykreslen pomocí <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="de451-295">A child component's content is rendered with a <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span>
* <span data-ttu-id="de451-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána v nadřazené komponentě.</span><span class="sxs-lookup"><span data-stu-id="de451-296"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent component.</span></span>

<span data-ttu-id="de451-297">Parametry jsou resetovány, protože nadřazená komponenta je znovu vygenerována <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> , když je volána, a podřízené součásti jsou zadány nové hodnoty parametrů.</span><span class="sxs-lookup"><span data-stu-id="de451-297">Parameters are reset because the parent component rerenders when <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called and new parameter values are supplied to the child component.</span></span>

<span data-ttu-id="de451-298">Vezměte v úvahu následující `Expander` komponentu:</span><span class="sxs-lookup"><span data-stu-id="de451-298">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="de451-299">Vykreslí podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="de451-299">Renders child content.</span></span>
* <span data-ttu-id="de451-300">Přepíná zobrazení podřízeného obsahu s parametrem součásti.</span><span class="sxs-lookup"><span data-stu-id="de451-300">Toggles showing child content with a component parameter.</span></span>

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

<span data-ttu-id="de451-301">`Expander`Komponenta je přidána do nadřazené komponenty, která může zavolat <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> :</span><span class="sxs-lookup"><span data-stu-id="de451-301">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

<span data-ttu-id="de451-302">Zpočátku se `Expander` komponenty chovají nezávisle při `Expanded` přepínání jejich vlastností.</span><span class="sxs-lookup"><span data-stu-id="de451-302">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="de451-303">Podřízené komponenty udržují své stavy podle očekávání.</span><span class="sxs-lookup"><span data-stu-id="de451-303">The child components maintain their states as expected.</span></span> <span data-ttu-id="de451-304">Když <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> je volána v nadřazeném prvku, `Expanded` parametr první podřízené komponenty se obnoví zpět na původní hodnotu ( `true` ).</span><span class="sxs-lookup"><span data-stu-id="de451-304">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="de451-305">Hodnota druhé `Expander` součásti `Expanded` není resetována, protože ve druhé komponentě není vykreslen žádný podřízený obsah.</span><span class="sxs-lookup"><span data-stu-id="de451-305">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="de451-306">Chcete-li zachovat stav v předchozím scénáři, použijte *soukromé pole* v `Expander` součásti k údržbě jeho přepnutého stavu.</span><span class="sxs-lookup"><span data-stu-id="de451-306">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="de451-307">Následující `Expander` součást:</span><span class="sxs-lookup"><span data-stu-id="de451-307">The following `Expander` component:</span></span>

* <span data-ttu-id="de451-308">Akceptuje `Expanded` hodnotu parametru komponenty z nadřazené položky.</span><span class="sxs-lookup"><span data-stu-id="de451-308">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="de451-309">Přiřadí hodnotu parametru komponenty k *privátnímu poli* ( `expanded` ) v [události s inicializací](xref:blazor/lifecycle#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="de451-309">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="de451-310">Pomocí soukromého pole udržuje stav interního přepínání.</span><span class="sxs-lookup"><span data-stu-id="de451-310">Uses the private field to maintain its internal toggle state.</span></span>

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

## <a name="partial-class-support"></a><span data-ttu-id="de451-311">Podpora částečné třídy</span><span class="sxs-lookup"><span data-stu-id="de451-311">Partial class support</span></span>

Razor<span data-ttu-id="de451-312">komponenty jsou generovány jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="de451-312"> components are generated as partial classes.</span></span> Razor<span data-ttu-id="de451-313">komponenty jsou vytvořeny jedním z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="de451-313"> components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="de451-314">Kód jazyka C# je definován v [`@code`][1] bloku s označením HTML a Razor kódem v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="de451-314">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="de451-315">šablony definují své Razor komponenty pomocí tohoto přístupu.</span><span class="sxs-lookup"><span data-stu-id="de451-315"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="de451-316">Kód jazyka C# je umístěn v souboru kódu na pozadí, který je definován jako částečná třída.</span><span class="sxs-lookup"><span data-stu-id="de451-316">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="de451-317">Následující příklad ukazuje výchozí `Counter` komponentu s [`@code`][1] blokem v aplikaci vygenerovanou ze Blazor šablony.</span><span class="sxs-lookup"><span data-stu-id="de451-317">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="de451-318">Kód HTML kódu Razor a kód jazyka C# jsou ve stejném souboru:</span><span class="sxs-lookup"><span data-stu-id="de451-318">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="de451-319">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-319">*Counter.razor*:</span></span>

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

<span data-ttu-id="de451-320">`Counter`Komponentu lze také vytvořit pomocí souboru kódu na pozadí s částečnou třídou:</span><span class="sxs-lookup"><span data-stu-id="de451-320">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="de451-321">*Čítač. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-321">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="de451-322">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="de451-322">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="de451-323">Podle potřeby přidejte všechny požadované obory názvů do souboru dílčí třídy.</span><span class="sxs-lookup"><span data-stu-id="de451-323">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="de451-324">Mezi obvyklé obory názvů používané Razor komponentami patří:</span><span class="sxs-lookup"><span data-stu-id="de451-324">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="de451-325">Zadat základní třídu</span><span class="sxs-lookup"><span data-stu-id="de451-325">Specify a base class</span></span>

<span data-ttu-id="de451-326">[`@inherits`][6]Direktiva se dá použít k určení základní třídy pro komponentu.</span><span class="sxs-lookup"><span data-stu-id="de451-326">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="de451-327">Následující příklad ukazuje, jak komponenta může dědit základní třídu, `BlazorRocksBase` k poskytnutí vlastností a metod komponenty.</span><span class="sxs-lookup"><span data-stu-id="de451-327">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="de451-328">Základní třída by měla být odvozena od třídy <xref:Microsoft.AspNetCore.Components.ComponentBase> .</span><span class="sxs-lookup"><span data-stu-id="de451-328">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="de451-329">*Stránky/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="de451-329">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="de451-330">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="de451-330">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="de451-331">Zadat atribut</span><span class="sxs-lookup"><span data-stu-id="de451-331">Specify an attribute</span></span>

<span data-ttu-id="de451-332">Atributy lze zadat v Razor součástech s [`@attribute`][7] direktivou.</span><span class="sxs-lookup"><span data-stu-id="de451-332">Attributes can be specified in Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="de451-333">Následující příklad používá [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atribut pro třídu komponenty:</span><span class="sxs-lookup"><span data-stu-id="de451-333">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="de451-334">Importovat součásti</span><span class="sxs-lookup"><span data-stu-id="de451-334">Import components</span></span>

<span data-ttu-id="de451-335">Obor názvů komponenty, která se vytvořila, Razor je založený na (v pořadí podle priority):</span><span class="sxs-lookup"><span data-stu-id="de451-335">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="de451-336">[`@namespace`][8]označení v Razor souboru (*. Razor*) značky ( `@namespace BlazorSample.MyNamespace` ).</span><span class="sxs-lookup"><span data-stu-id="de451-336">[`@namespace`][8] designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="de451-337">Projekt `RootNamespace` v souboru projektu ( `<RootNamespace>BlazorSample</RootNamespace>` ).</span><span class="sxs-lookup"><span data-stu-id="de451-337">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="de451-338">Název projektu, pořízený z názvu souboru projektu (*. csproj*) a cesta z kořenového adresáře projektu ke komponentě.</span><span class="sxs-lookup"><span data-stu-id="de451-338">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="de451-339">Rozhraní například překládá *{root]/pages/index.Razor* (*BlazorSample. csproj*) na obor názvů `BlazorSample.Pages` .</span><span class="sxs-lookup"><span data-stu-id="de451-339">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="de451-340">Komponenty následují pravidla vazeb názvů C#.</span><span class="sxs-lookup"><span data-stu-id="de451-340">Components follow C# name binding rules.</span></span> <span data-ttu-id="de451-341">Pro `Index` komponentu v tomto příkladu komponenty v oboru jsou všechny komponenty:</span><span class="sxs-lookup"><span data-stu-id="de451-341">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="de451-342">Ve stejné složce *stránky*.</span><span class="sxs-lookup"><span data-stu-id="de451-342">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="de451-343">Komponenty v kořenu projektu, které explicitně neurčují jiný obor názvů.</span><span class="sxs-lookup"><span data-stu-id="de451-343">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="de451-344">Komponenty definované v jiném oboru názvů se přenesou do rozsahu Razor [`@using`][2] direktivy using.</span><span class="sxs-lookup"><span data-stu-id="de451-344">Components defined in a different namespace are brought into scope using Razor's [`@using`][2] directive.</span></span>

<span data-ttu-id="de451-345">Pokud jiná komponenta, `NavMenu.razor` existuje ve složce *BlazorSample/Shared/* Folder, lze komponentu použít v `Index.razor` příkazu s následujícím [`@using`][2] příkazem:</span><span class="sxs-lookup"><span data-stu-id="de451-345">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following [`@using`][2] statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="de451-346">Na součásti lze také odkazovat pomocí jejich plně kvalifikovaných názvů, které nevyžadují [`@using`][2] direktivu:</span><span class="sxs-lookup"><span data-stu-id="de451-346">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="de451-347">`global::`Kvalifikace není podporovaná.</span><span class="sxs-lookup"><span data-stu-id="de451-347">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="de451-348">Import komponent s aliasy, které [používají](/dotnet/csharp/language-reference/keywords/using-statement) příkazy (například `@using Foo = Bar` ), není podporován.</span><span class="sxs-lookup"><span data-stu-id="de451-348">Importing components with aliased [using](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="de451-349">Částečně kvalifikované názvy nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="de451-349">Partially qualified names aren't supported.</span></span> <span data-ttu-id="de451-350">Například přidání `@using BlazorSample` a odkazování na `NavMenu` komponentu ( `NavMenu.razor` ) s `<Shared.NavMenu></Shared.NavMenu>` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="de451-350">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="de451-351">Podmíněné atributy elementu HTML</span><span class="sxs-lookup"><span data-stu-id="de451-351">Conditional HTML element attributes</span></span>

<span data-ttu-id="de451-352">Atributy elementu HTML jsou podmíněně vykresleny na základě hodnoty .NET.</span><span class="sxs-lookup"><span data-stu-id="de451-352">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="de451-353">Pokud je hodnota `false` nebo `null` , atribut není vykreslen.</span><span class="sxs-lookup"><span data-stu-id="de451-353">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="de451-354">Pokud je hodnota `true` , je vygenerována hodnota atributu minimalizovaný.</span><span class="sxs-lookup"><span data-stu-id="de451-354">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="de451-355">V následujícím příkladu určuje, `IsCompleted` zda `checked` je vykreslena v kódu elementu:</span><span class="sxs-lookup"><span data-stu-id="de451-355">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="de451-356">Pokud `IsCompleted` je `true` , zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="de451-356">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="de451-357">Pokud `IsCompleted` je `false` , zaškrtávací políčko je vykresleno jako:</span><span class="sxs-lookup"><span data-stu-id="de451-357">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="de451-358">Další informace naleznete v tématu <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="de451-358">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="de451-359">Některé atributy HTML, jako je například [Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), nefungují správně, pokud je typ .NET `bool` .</span><span class="sxs-lookup"><span data-stu-id="de451-359">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="de451-360">V těchto případech použijte `string` typ místo typu `bool` .</span><span class="sxs-lookup"><span data-stu-id="de451-360">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="de451-361">Nezpracovaný kód HTML</span><span class="sxs-lookup"><span data-stu-id="de451-361">Raw HTML</span></span>

<span data-ttu-id="de451-362">Řetězce jsou obvykle vykreslovány pomocí textových uzlů modelu DOM, což znamená, že všechny značky, které mohou obsahovat, se ignorují a považují se za text literálu.</span><span class="sxs-lookup"><span data-stu-id="de451-362">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="de451-363">Chcete-li vykreslit nezpracovaný kód HTML, zabalte obsah HTML do `MarkupString` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="de451-363">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="de451-364">Hodnota je analyzována jako HTML nebo SVG a vložena do modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="de451-364">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="de451-365">Vykreslování nezpracovaného HTML vytvořeného z jakéhokoli nedůvěryhodného zdroje je **bezpečnostní riziko** a mělo by se jim vyhnout!</span><span class="sxs-lookup"><span data-stu-id="de451-365">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="de451-366">Následující příklad ukazuje použití `MarkupString` typu pro přidání bloku statického obsahu HTML do vykresleného výstupu komponenty:</span><span class="sxs-lookup"><span data-stu-id="de451-366">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="de451-367">Kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="de451-367">Cascading values and parameters</span></span>

<span data-ttu-id="de451-368">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="de451-368">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="de451-369">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="de451-369">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="de451-370">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="de451-370">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="de451-371">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="de451-371">Theme example</span></span>

<span data-ttu-id="de451-372">V následujícím příkladu z ukázkové aplikace `ThemeInfo` Třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="de451-372">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="de451-373">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="de451-373">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="de451-374">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="de451-374">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="de451-375"><xref:Microsoft.AspNetCore.Components.CascadingValue%601>Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="de451-375">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="de451-376">Například ukázková aplikace určuje informace o motivu ( `ThemeInfo` ) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="de451-376">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="de451-377">`ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="de451-377">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="de451-378">Každá odvozená komponenta může tuto vlastnost spotřebovat prostřednictvím `ThemeInfo` kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="de451-378">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="de451-379">`CascadingValuesParametersLayout`část</span><span class="sxs-lookup"><span data-stu-id="de451-379">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="de451-380">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributu.</span><span class="sxs-lookup"><span data-stu-id="de451-380">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="de451-381">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="de451-381">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="de451-382">V ukázkové aplikaci `CascadingValuesParametersTheme` váže komponenta `ThemeInfo` kaskádovou hodnotu k kaskádovým parametrům.</span><span class="sxs-lookup"><span data-stu-id="de451-382">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="de451-383">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="de451-383">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="de451-384">`CascadingValuesParametersTheme`část</span><span class="sxs-lookup"><span data-stu-id="de451-384">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="de451-385">Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> řetězec pro každou <xref:Microsoft.AspNetCore.Components.CascadingValue%601> komponentu a odpovídající [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="de451-385">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="de451-386">V následujícím příkladu jsou dvě <xref:Microsoft.AspNetCore.Components.CascadingValue%601> komponenty kaskádovitě různé instance `MyCascadingType` podle názvu:</span><span class="sxs-lookup"><span data-stu-id="de451-386">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="de451-387">V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="de451-387">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="de451-388">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="de451-388">TabSet example</span></span>

<span data-ttu-id="de451-389">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="de451-389">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="de451-390">Podívejte se například na následující příklad *TabSet* v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="de451-390">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="de451-391">Ukázková aplikace má `ITab` rozhraní, které implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="de451-391">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="de451-392">`CascadingValuesParametersTabSet`Komponenta používá `TabSet` komponentu, která obsahuje několik `Tab` komponent:</span><span class="sxs-lookup"><span data-stu-id="de451-392">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="de451-393">Podřízené `Tab` součásti nejsou explicitně předány jako parametry do `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="de451-393">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="de451-394">Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="de451-394">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="de451-395">`TabSet`Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, `TabSet` komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta podřízenými `Tab` součástmi.</span><span class="sxs-lookup"><span data-stu-id="de451-395">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="de451-396">`TabSet`část</span><span class="sxs-lookup"><span data-stu-id="de451-396">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="de451-397">Podřízené `Tab` komponenty zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do `TabSet` souřadnice a, na které karty jsou aktivní.</span><span class="sxs-lookup"><span data-stu-id="de451-397">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="de451-398">`Tab`část</span><span class="sxs-lookup"><span data-stu-id="de451-398">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor<span data-ttu-id="de451-399">šablony</span><span class="sxs-lookup"><span data-stu-id="de451-399"> templates</span></span>

<span data-ttu-id="de451-400">Fragmenty vykreslování lze definovat pomocí Razor syntaxe šablony.</span><span class="sxs-lookup"><span data-stu-id="de451-400">Render fragments can be defined using Razor template syntax.</span></span> Razor<span data-ttu-id="de451-401">Šablony představují způsob, jak definovat fragment uživatelského rozhraní a předpokládat následující formát:</span><span class="sxs-lookup"><span data-stu-id="de451-401"> templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="de451-402">Následující příklad ukazuje, jak zadat <xref:Microsoft.AspNetCore.Components.RenderFragment> a <xref:Microsoft.AspNetCore.Components.RenderFragment%601> hodnoty a vykreslit šablony přímo v součásti.</span><span class="sxs-lookup"><span data-stu-id="de451-402">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="de451-403">Fragmenty vykreslování mohou být také předány jako argumenty [součástem šablon](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="de451-403">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

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

<span data-ttu-id="de451-404">Vykreslený výstup předchozího kódu:</span><span class="sxs-lookup"><span data-stu-id="de451-404">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="de451-405">Obrázky ve formátu SVG (Scalable Vector Graphics)</span><span class="sxs-lookup"><span data-stu-id="de451-405">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="de451-406">Vzhledem k tomu, že Blazor VYKRESLUJE HTML, obrázky podporované prohlížečem, včetně obrázků SVG (Scalable Vector Graphics) (*. SVG*), jsou podporovány prostřednictvím `<img>` značky:</span><span class="sxs-lookup"><span data-stu-id="de451-406">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="de451-407">Podobně jsou obrázky SVG podporovány v pravidlech CSS souboru šablony stylů (*. CSS*):</span><span class="sxs-lookup"><span data-stu-id="de451-407">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="de451-408">Vložené značky SVG se však ve všech scénářích nepodporují.</span><span class="sxs-lookup"><span data-stu-id="de451-408">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="de451-409">Pokud `<svg>` značku přímo umístíte do souboru komponenty (*. Razor*), podporuje se základní vykreslování obrázků, ale mnoho pokročilých scénářů ještě není podporováno.</span><span class="sxs-lookup"><span data-stu-id="de451-409">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="de451-410">Například `<use>` značky nejsou aktuálně dodržovány a [`@bind`][10] nelze je použít s některými značkami SVG.</span><span class="sxs-lookup"><span data-stu-id="de451-410">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="de451-411">Další informace naleznete v tématu [Podpora SVG in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span><span class="sxs-lookup"><span data-stu-id="de451-411">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de451-412">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="de451-412">Additional resources</span></span>

* <span data-ttu-id="de451-413"><xref:security/blazor/server/threat-mitigation>&ndash;Obsahuje doprovodné materiály k vytváření Blazor Serverové aplikace, které se musí soupeří s vyčerpáním prostředků</span><span class="sxs-lookup"><span data-stu-id="de451-413"><xref:security/blazor/server/threat-mitigation> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
