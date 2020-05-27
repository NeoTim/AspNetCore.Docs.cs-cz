---
<span data-ttu-id="13b92-101">title: ' ASP.NET Core Blazor Layouts ' Author: Description: ' Naučte se vytvářet opakovaně použitelné součásti rozložení pro Blazor aplikace. '</span><span class="sxs-lookup"><span data-stu-id="13b92-101">title: 'ASP.NET Core Blazor layouts' author: description: 'Learn how to create reusable layout components for Blazor apps.'</span></span>
<span data-ttu-id="13b92-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="13b92-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="13b92-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="13b92-103">'Blazor'</span></span>
- <span data-ttu-id="13b92-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="13b92-104">'Identity'</span></span>
- <span data-ttu-id="13b92-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="13b92-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="13b92-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="13b92-106">'Razor'</span></span>
- <span data-ttu-id="13b92-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="13b92-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="13b92-108">ASP.NET Core Blazor rozložení</span><span class="sxs-lookup"><span data-stu-id="13b92-108">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="13b92-109">Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13b92-109">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="13b92-110">Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="13b92-110">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="13b92-111">Kopírování kódu těchto prvků do všech součástí aplikace není účinný přístup.</span><span class="sxs-lookup"><span data-stu-id="13b92-111">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="13b92-112">Pokaždé, když jeden z elementů vyžaduje aktualizaci, musí být každá součást aktualizována.</span><span class="sxs-lookup"><span data-stu-id="13b92-112">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="13b92-113">Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu.</span><span class="sxs-lookup"><span data-stu-id="13b92-113">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="13b92-114">*Rozložení* řeší tento problém.</span><span class="sxs-lookup"><span data-stu-id="13b92-114">*Layouts* solve this problem.</span></span>

<span data-ttu-id="13b92-115">Technicky, rozložení je pouze jiná komponenta.</span><span class="sxs-lookup"><span data-stu-id="13b92-115">Technically, a layout is just another component.</span></span> <span data-ttu-id="13b92-116">Rozložení je definováno v Razor šabloně nebo v kódu jazyka C# a může používat [datové vazby](xref:blazor/data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.</span><span class="sxs-lookup"><span data-stu-id="13b92-116">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="13b92-117">Chcete-li změnit *komponentu* na *rozložení*, součást:</span><span class="sxs-lookup"><span data-stu-id="13b92-117">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="13b92-118">Dědí z <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , který definuje <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> vlastnost pro vykreslený obsah v rozložení.</span><span class="sxs-lookup"><span data-stu-id="13b92-118">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="13b92-119">Používá Razor syntaxi `@Body` k určení umístění v označení rozložení, kde je obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="13b92-119">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="13b92-120">Následující ukázka kódu ukazuje Razor šablonu komponenty rozložení *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="13b92-120">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="13b92-121">Rozložení dědí <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> a nastavuje `@Body` mezi navigačním panelem a zápatím:</span><span class="sxs-lookup"><span data-stu-id="13b92-121">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="13b92-122">V aplikaci založené na jedné ze Blazor šablon aplikací `MainLayout` je součást (*MainLayout. Razor*) ve *sdílené* složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="13b92-122">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="13b92-123">Výchozí rozložení</span><span class="sxs-lookup"><span data-stu-id="13b92-123">Default layout</span></span>

<span data-ttu-id="13b92-124">Zadejte výchozí rozložení aplikace v <xref:Microsoft.AspNetCore.Components.Routing.Router> komponentě v souboru App *. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="13b92-124">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's *App.razor* file.</span></span> <span data-ttu-id="13b92-125">Následující <xref:Microsoft.AspNetCore.Components.Routing.Router> Komponenta, která je poskytována výchozími Blazor šablonami, nastaví výchozí rozložení na `MainLayout` součást:</span><span class="sxs-lookup"><span data-stu-id="13b92-125">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="13b92-126">Chcete-li zadat výchozí rozložení <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsahu, zadejte <xref:Microsoft.AspNetCore.Components.LayoutView> pro <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsah:</span><span class="sxs-lookup"><span data-stu-id="13b92-126">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="13b92-127">Další informace o <xref:Microsoft.AspNetCore.Components.Routing.Router> komponentě naleznete v tématu <xref:blazor/routing> .</span><span class="sxs-lookup"><span data-stu-id="13b92-127">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="13b92-128">Určení rozložení jako výchozího rozložení ve směrovači je užitečný postup, protože je možné ho přepsat pro jednotlivé komponenty nebo pro jednotlivé složky.</span><span class="sxs-lookup"><span data-stu-id="13b92-128">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="13b92-129">Upřednostňujete použití směrovače k nastavení výchozího rozložení aplikace, protože se jedná o nejobecnější postup.</span><span class="sxs-lookup"><span data-stu-id="13b92-129">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="13b92-130">Určení rozložení v součásti</span><span class="sxs-lookup"><span data-stu-id="13b92-130">Specify a layout in a component</span></span>

<span data-ttu-id="13b92-131">Použijte Razor direktivu `@layout` pro použití rozložení na komponentu.</span><span class="sxs-lookup"><span data-stu-id="13b92-131">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="13b92-132">Kompilátor převede `@layout` na <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , který je použit pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="13b92-132">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="13b92-133">Obsah následující `MasterList` komponenty je vložen do `MasterLayout` pozice v umístění `@Body` :</span><span class="sxs-lookup"><span data-stu-id="13b92-133">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="13b92-134">Určením rozložení přímo v součásti dojde k přepsání *výchozí sady rozložení* ve směrovači nebo `@layout` direktivy importované z *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="13b92-134">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="13b92-135">Centralizovaný výběr rozložení</span><span class="sxs-lookup"><span data-stu-id="13b92-135">Centralized layout selection</span></span>

<span data-ttu-id="13b92-136">Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="13b92-136">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="13b92-137">Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech Razor šablonách ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="13b92-137">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="13b92-138">Proto soubor *_Imports. Razor* `@layout MyCoolLayout` , který obsahuje, zajistí, aby všechny součásti ve složce používaly `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="13b92-138">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="13b92-139">Není nutné opakovaně přidávat `@layout MyCoolLayout` do všech souborů *. Razor* v rámci složky a podsložek.</span><span class="sxs-lookup"><span data-stu-id="13b92-139">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="13b92-140">`@using`direktivy jsou také aplikovány na komponenty stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="13b92-140">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="13b92-141">Následující soubor *_Imports. Razor* importuje:</span><span class="sxs-lookup"><span data-stu-id="13b92-141">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="13b92-142">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="13b92-142">`MyCoolLayout`.</span></span>
* <span data-ttu-id="13b92-143">Všechny Razor součásti ve stejné složce a v jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="13b92-143">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="13b92-144">`BlazorApp1.Data`Obor názvů.</span><span class="sxs-lookup"><span data-stu-id="13b92-144">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="13b92-145">Soubor *_Imports. Razor* je podobný [souboru _ViewImports. cshtml pro Razor zobrazení a stránky](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro Razor soubory komponent.</span><span class="sxs-lookup"><span data-stu-id="13b92-145">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="13b92-146">Určení rozložení v *_Imports. Razor* přepíše rozložení zadané jako *výchozí rozložení*směrovače.</span><span class="sxs-lookup"><span data-stu-id="13b92-146">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="13b92-147">Vnořená rozložení</span><span class="sxs-lookup"><span data-stu-id="13b92-147">Nested layouts</span></span>

<span data-ttu-id="13b92-148">Aplikace se můžou skládat z vnořených rozložení.</span><span class="sxs-lookup"><span data-stu-id="13b92-148">Apps can consist of nested layouts.</span></span> <span data-ttu-id="13b92-149">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="13b92-149">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="13b92-150">Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.</span><span class="sxs-lookup"><span data-stu-id="13b92-150">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="13b92-151">Následující příklad ukazuje, jak použít vnořená rozložení.</span><span class="sxs-lookup"><span data-stu-id="13b92-151">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="13b92-152">Soubor *EpisodesComponent. Razor* je komponenta, která se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="13b92-152">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="13b92-153">Komponenta odkazuje na `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="13b92-153">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="13b92-154">Soubor *MasterListLayout. Razor* poskytuje `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="13b92-154">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="13b92-155">Rozložení odkazuje na jiné rozložení, `MasterLayout` , kde je vykresleno.</span><span class="sxs-lookup"><span data-stu-id="13b92-155">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="13b92-156">`EpisodesComponent`je vykreslen, kde `@Body` se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="13b92-156">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="13b92-157">Nakonec `MasterLayout` v *MasterLayout. Razor* obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí.</span><span class="sxs-lookup"><span data-stu-id="13b92-157">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="13b92-158">`MasterListLayout`tam, `EpisodesComponent` kde se zobrazí, se vykreslí `@Body` :</span><span class="sxs-lookup"><span data-stu-id="13b92-158">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="13b92-159">Sdílení Razor rozložení stránek pomocí integrovaných komponent</span><span class="sxs-lookup"><span data-stu-id="13b92-159">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="13b92-160">Pokud jsou směrovatelný komponenty integrovány do Razor aplikace Pages, lze použít sdílené rozložení aplikace spolu s komponentami.</span><span class="sxs-lookup"><span data-stu-id="13b92-160">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="13b92-161">Další informace naleznete v tématu <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="13b92-161">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13b92-162">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="13b92-162">Additional resources</span></span>

* <xref:mvc/views/layout>
