---
title: Rozložení v ASP.NET Core
author: ardalis
description: Naučte se používat běžné rozložení, direktivy sdílení a spouštět společný kód před vykreslením zobrazení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/layout
ms.openlocfilehash: 4d5032f02db28341d7781dd57d58d776636fd16d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020441"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="3dd05-103">Rozložení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dd05-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="3dd05-104">[Steve Smith](https://ardalis.com/) a [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="3dd05-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="3dd05-105">Stránky a zobrazení často sdílí vizuální a programové prvky.</span><span class="sxs-lookup"><span data-stu-id="3dd05-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="3dd05-106">Tento článek ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="3dd05-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="3dd05-107">Použijte společná rozložení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-107">Use common layouts.</span></span>
* <span data-ttu-id="3dd05-108">Direktivy Share.</span><span class="sxs-lookup"><span data-stu-id="3dd05-108">Share directives.</span></span>
* <span data-ttu-id="3dd05-109">Před vykreslováním stránek nebo zobrazení spouštějte běžný kód.</span><span class="sxs-lookup"><span data-stu-id="3dd05-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="3dd05-110">Tento dokument popisuje rozložení dvou různých přístupů k ASP.NET Core MVC: Razor stránky a řadiče se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="3dd05-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="3dd05-111">Rozdíly jsou pro toto téma minimální:</span><span class="sxs-lookup"><span data-stu-id="3dd05-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="3dd05-112">RazorStránky jsou ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="3dd05-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="3dd05-113">Řadiče se zobrazeními používají pro zobrazení složku *zobrazení* .</span><span class="sxs-lookup"><span data-stu-id="3dd05-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="3dd05-114">Co je rozložení</span><span class="sxs-lookup"><span data-stu-id="3dd05-114">What is a Layout</span></span>

<span data-ttu-id="3dd05-115">Většina webových aplikací má společné rozložení, které uživatelům poskytuje konzistentní prostředí při přechodu ze stránky na stránku.</span><span class="sxs-lookup"><span data-stu-id="3dd05-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="3dd05-116">Rozložení obvykle zahrnuje běžné prvky uživatelského rozhraní, jako jsou záhlaví aplikace, navigace nebo prvky nabídky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="3dd05-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Příklad rozložení stránky](layout/_static/page-layout.png)

<span data-ttu-id="3dd05-118">Mnoho stránek v aplikaci také často používá běžné struktury HTML, jako jsou skripty a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="3dd05-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="3dd05-119">Všechny tyto sdílené prvky mohou být definovány v souboru *rozložení* , na který lze následně odkazovat jakýmkoli zobrazením použitým v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3dd05-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="3dd05-120">Rozložení omezují výskyt duplicitního kódu v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="3dd05-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="3dd05-121">Podle konvence je výchozím rozložením pro aplikaci ASP.NET Core název *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3dd05-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="3dd05-122">Soubory rozložení pro nové projekty ASP.NET Core vytvořené pomocí šablon jsou:</span><span class="sxs-lookup"><span data-stu-id="3dd05-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="3dd05-123">RazorStránky: *stránky/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3dd05-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Složka stránky v Průzkumník řešení](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="3dd05-125">Kontroler se zobrazeními: *views/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3dd05-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Složka zobrazení v Průzkumník řešení](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="3dd05-127">Rozložení definuje šablonu na nejvyšší úrovni pro zobrazení v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3dd05-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="3dd05-128">Aplikace nevyžadují rozložení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-128">Apps don't require a layout.</span></span> <span data-ttu-id="3dd05-129">Aplikace mohou definovat více než jedno rozložení s různými zobrazeními určujícími různá rozložení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="3dd05-130">Následující kód ukazuje soubor rozložení pro šablonu vytvořenou v projektu pomocí kontroleru a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3dd05-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="3dd05-131">Určení rozložení</span><span class="sxs-lookup"><span data-stu-id="3dd05-131">Specifying a Layout</span></span>

<span data-ttu-id="3dd05-132">Razorzobrazení mají `Layout` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3dd05-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="3dd05-133">Jednotlivá zobrazení určují rozložení nastavením této vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3dd05-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="3dd05-134">Zadané rozložení může použít úplnou cestu (například */Pages/Shared/_Layout. cshtml* nebo */views/Shared/_Layout. cshtml*) nebo částečný název (například: `_Layout` ).</span><span class="sxs-lookup"><span data-stu-id="3dd05-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="3dd05-135">Pokud je k dispozici částečný název, Razor modul zobrazení vyhledá soubor rozložení pomocí standardního procesu zjišťování.</span><span class="sxs-lookup"><span data-stu-id="3dd05-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="3dd05-136">Složka, ve které existuje metoda obslužné rutiny (nebo kontrolér), je nejdříve prohledána a za ní následuje *sdílená* složka.</span><span class="sxs-lookup"><span data-stu-id="3dd05-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="3dd05-137">Tento proces zjišťování je stejný jako proces, který se používá ke zjišťování [částečných zobrazení](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="3dd05-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="3dd05-138">Ve výchozím nastavení musí každé rozložení volat `RenderBody` .</span><span class="sxs-lookup"><span data-stu-id="3dd05-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="3dd05-139">Všude `RenderBody` , kde je umístěno volání, se vykreslí obsah zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="3dd05-140">Oddíly</span><span class="sxs-lookup"><span data-stu-id="3dd05-140">Sections</span></span>

<span data-ttu-id="3dd05-141">Rozložení může volitelně odkazovat na jeden nebo více *oddílů*voláním `RenderSection` .</span><span class="sxs-lookup"><span data-stu-id="3dd05-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="3dd05-142">Oddíly poskytují způsob, jak uspořádat, kde by měly být umístěny určité prvky stránky.</span><span class="sxs-lookup"><span data-stu-id="3dd05-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="3dd05-143">Každé volání, které umožňuje `RenderSection` určit, zda je tato část povinná nebo volitelná:</span><span class="sxs-lookup"><span data-stu-id="3dd05-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="3dd05-144">Pokud není požadovaný oddíl nalezen, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="3dd05-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="3dd05-145">Jednotlivá zobrazení určují obsah, který se má vykreslit v rámci oddílu pomocí `@section` Razor syntaxe.</span><span class="sxs-lookup"><span data-stu-id="3dd05-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="3dd05-146">Pokud stránka nebo zobrazení definují oddíl, je nutné jej vykreslit (nebo dojde k chybě).</span><span class="sxs-lookup"><span data-stu-id="3dd05-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="3dd05-147">Příklad `@section` definice v Razor zobrazení stránky:</span><span class="sxs-lookup"><span data-stu-id="3dd05-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="3dd05-148">V předchozím kódu se *skripty/main.js* přidaly do `scripts` oddílu na stránce nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="3dd05-149">Jiné stránky nebo zobrazení ve stejné aplikaci pravděpodobně nevyžadují tento skript a nedefinují oddíl Scripts.</span><span class="sxs-lookup"><span data-stu-id="3dd05-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="3dd05-150">Následující kód používá [pomocníka částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) k vykreslování *_ValidationScriptsPartial. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3dd05-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="3dd05-151">Předchozí kód byl vygenerován pomocí [generování uživatelského Identity rozhraní ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="3dd05-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="3dd05-152">Oddíly definované na stránce nebo zobrazení jsou k dispozici pouze na stránce jejího okamžitého rozložení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="3dd05-153">Nelze na ně odkazovat z částečných, zobrazení součástí nebo jiných částí systému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="3dd05-154">Ignorují se oddíly.</span><span class="sxs-lookup"><span data-stu-id="3dd05-154">Ignoring sections</span></span>

<span data-ttu-id="3dd05-155">Ve výchozím nastavení musí být tělo a všechny oddíly na stránce obsahu vykresleny na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="3dd05-156">RazorModul zobrazení tento modul vynutil sledováním, zda byl text a jednotlivé oddíly vykresleny.</span><span class="sxs-lookup"><span data-stu-id="3dd05-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="3dd05-157">Chcete-li, aby modul zobrazení mohl ignorovat tělo nebo oddíly, zavolejte `IgnoreBody` `IgnoreSection` metody a.</span><span class="sxs-lookup"><span data-stu-id="3dd05-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="3dd05-158">Tělo a každý oddíl Razor stránky musí být vykresleny nebo ignorovány.</span><span class="sxs-lookup"><span data-stu-id="3dd05-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="3dd05-159">Import sdílených direktiv</span><span class="sxs-lookup"><span data-stu-id="3dd05-159">Importing Shared Directives</span></span>

<span data-ttu-id="3dd05-160">Zobrazení a stránky můžou pomocí Razor direktiv importovat obory názvů a používat [vkládání závislostí](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="3dd05-160">Views and pages can use Razor directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="3dd05-161">Direktivy sdílené pomocí mnoha zobrazení mohou být zadány v běžném souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3dd05-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="3dd05-162">`_ViewImports`Soubor podporuje následující direktivy:</span><span class="sxs-lookup"><span data-stu-id="3dd05-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="3dd05-163">Soubor nepodporuje jiné Razor funkce, jako jsou třeba definice funkcí a oddílů.</span><span class="sxs-lookup"><span data-stu-id="3dd05-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="3dd05-164">Vzorový `_ViewImports.cshtml` soubor:</span><span class="sxs-lookup"><span data-stu-id="3dd05-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="3dd05-165">Soubor *_ViewImports. cshtml* pro aplikaci ASP.NET Core MVC je obvykle umístěn ve složce *Pages* (nebo *views*).</span><span class="sxs-lookup"><span data-stu-id="3dd05-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="3dd05-166">*_ViewImports soubor. cshtml* lze umístit do jakékoli složky. v takovém případě bude použito pouze na stránky nebo zobrazení v této složce a jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="3dd05-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="3dd05-167">`_ViewImports`soubory jsou zpracovávány na kořenové úrovni a následně pro každou složku, která je v umístění stránky nebo samotného zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3dd05-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="3dd05-168">`_ViewImports`nastavení zadaná na kořenové úrovni lze přepsat na úrovni složky.</span><span class="sxs-lookup"><span data-stu-id="3dd05-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="3dd05-169">Předpokládejme například:</span><span class="sxs-lookup"><span data-stu-id="3dd05-169">For example, suppose:</span></span>

* <span data-ttu-id="3dd05-170">*_ViewImports soubor. cshtml* na kořenové úrovni zahrnuje `@model MyModel1` a `@addTagHelper *, MyTagHelper1` .</span><span class="sxs-lookup"><span data-stu-id="3dd05-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="3dd05-171">Podsložka *_ViewImports soubor. cshtml* obsahuje `@model MyModel2` a `@addTagHelper *, MyTagHelper2` .</span><span class="sxs-lookup"><span data-stu-id="3dd05-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="3dd05-172">Stránky a zobrazení v podsložce budou mít přístup k oběma pomocníkům značek i k `MyModel2` modelu.</span><span class="sxs-lookup"><span data-stu-id="3dd05-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="3dd05-173">Pokud je v hierarchii souborů nalezeno více *_ViewImports. cshtml* souborů, kombinované chování direktiv jsou:</span><span class="sxs-lookup"><span data-stu-id="3dd05-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="3dd05-174">`@addTagHelper`, `@removeTagHelper` : všechny spuštěné, v pořadí</span><span class="sxs-lookup"><span data-stu-id="3dd05-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="3dd05-175">`@tagHelperPrefix`: nejbližší z nich přepisuje všechny ostatní.</span><span class="sxs-lookup"><span data-stu-id="3dd05-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="3dd05-176">`@model`: nejbližší z nich přepisuje všechny ostatní.</span><span class="sxs-lookup"><span data-stu-id="3dd05-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="3dd05-177">`@inherits`: nejbližší z nich přepisuje všechny ostatní.</span><span class="sxs-lookup"><span data-stu-id="3dd05-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="3dd05-178">`@using`: vše je zahrnuto; duplicity se ignorují.</span><span class="sxs-lookup"><span data-stu-id="3dd05-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="3dd05-179">`@inject`: pro každou vlastnost, která je nejblíže k zobrazení, potlačí všechny ostatní se stejným názvem vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3dd05-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="3dd05-180">Spuštění kódu před každým zobrazením</span><span class="sxs-lookup"><span data-stu-id="3dd05-180">Running Code Before Each View</span></span>

<span data-ttu-id="3dd05-181">Kód, který musí být spuštěn před každým zobrazením nebo stránkou, by měl být umístěn v souboru *_ViewStart. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3dd05-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="3dd05-182">Podle konvence je soubor *_ViewStart. cshtml* umístěný ve složce *Pages* (nebo *views*).</span><span class="sxs-lookup"><span data-stu-id="3dd05-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="3dd05-183">Příkazy uvedené v *_ViewStart. cshtml* jsou spouštěny před všemi úplnými zobrazeními (nikoli rozloženími, nikoli v částečných zobrazeních).</span><span class="sxs-lookup"><span data-stu-id="3dd05-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="3dd05-184">Podobně jako [ViewImports. cshtml](xref:mvc/views/layout#viewimports)je *_ViewStart. cshtml* hierarchicky.</span><span class="sxs-lookup"><span data-stu-id="3dd05-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="3dd05-185">Je-li soubor *_ViewStart. cshtml* definován ve složce zobrazení nebo stránky, bude spuštěn po jednom definovaném kořenu složky *stránky* (nebo *zobrazení*) (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="3dd05-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="3dd05-186">Ukázkový *_ViewStart soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3dd05-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="3dd05-187">Výše uvedený soubor určuje, že všechna zobrazení budou používat rozvržení *_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3dd05-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="3dd05-188">*_ViewStart. cshtml* a *_ViewImports. cshtml* nejsou **obvykle umístěny** ve složce */Pages/Shared* (nebo */views/Shared*).</span><span class="sxs-lookup"><span data-stu-id="3dd05-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="3dd05-189">Verze těchto souborů na úrovni aplikace by měly být umístěné přímo ve složce */Pages* (nebo */views*).</span><span class="sxs-lookup"><span data-stu-id="3dd05-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
