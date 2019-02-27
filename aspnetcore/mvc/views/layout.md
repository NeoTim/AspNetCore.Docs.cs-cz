---
title: Rozložení v ASP.NET Core
author: ardalis
description: Zjistěte, jak používat společné rozložení, sdílet direktivy a spustit běžné kód před vykreslení zobrazení v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 02/26/2019
uid: mvc/views/layout
ms.openlocfilehash: 7a60ee15e688d6f0e531302457604fa759213758
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899239"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="f1a08-103">Rozložení v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f1a08-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="f1a08-104">Podle [Steve Smith](https://ardalis.com/) a [společnosti Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="f1a08-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="f1a08-105">Zobrazení stránky a často sdílení visual a programové prvků.</span><span class="sxs-lookup"><span data-stu-id="f1a08-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="f1a08-106">Tento článek ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="f1a08-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="f1a08-107">Použití běžných rozložení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-107">Use common layouts.</span></span>
* <span data-ttu-id="f1a08-108">Sdílet direktivy.</span><span class="sxs-lookup"><span data-stu-id="f1a08-108">Share directives.</span></span>
* <span data-ttu-id="f1a08-109">Spusťte společný kód před vykreslování stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="f1a08-110">Tento dokument popisuje rozložení pro dva různé přístupy k ASP.NET Core MVC: A řadiče se zobrazeními Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f1a08-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: Razor Pages and controllers with views.</span></span> <span data-ttu-id="f1a08-111">Pro toto téma jsou minimální rozdíly:</span><span class="sxs-lookup"><span data-stu-id="f1a08-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="f1a08-112">Stránky Razor jsou v *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="f1a08-112">Razor Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="f1a08-113">Kontrolery se používá k zobrazení *zobrazení* složku pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="f1a08-114">Co je rozložení</span><span class="sxs-lookup"><span data-stu-id="f1a08-114">What is a Layout</span></span>

<span data-ttu-id="f1a08-115">Většina webových aplikací mají společné rozložení, který poskytuje uživatelům konzistentní prostředí při práci ze stránky na stránku.</span><span class="sxs-lookup"><span data-stu-id="f1a08-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="f1a08-116">Rozložení obvykle zahrnuje společné prvky uživatelského rozhraní, jako je například aplikace záhlaví, navigaci nebo prvky nabídky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="f1a08-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Příklad rozložení stránky](layout/_static/page-layout.png)

<span data-ttu-id="f1a08-118">Společné struktury HTML, jako jsou skripty a šablony stylů také často používá mnoho stránek v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f1a08-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="f1a08-119">Všechny tyto sdílené prvky mohou být definovány v *rozložení* soubor, který může odkazovat ve všech zobrazeních použít v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f1a08-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="f1a08-120">Rozložení snížit duplicitního kódu v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="f1a08-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="f1a08-121">Podle konvence je výchozí rozložení aplikace ASP.NET Core s názvem *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f1a08-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml*.</span></span> <span data-ttu-id="f1a08-122">Soubor rozložení pro nové projekty ASP.NET Core, které jsou vytvořené pomocí šablon:</span><span class="sxs-lookup"><span data-stu-id="f1a08-122">The layout file for new ASP.NET Core projects created with the templates:</span></span>

* <span data-ttu-id="f1a08-123">Stránky Razor: *Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a08-123">Razor Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![stránky složku v Průzkumníku řešení](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="f1a08-125">Kontroler zobrazení: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1a08-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

 ![zobrazení složky v Průzkumníku řešení](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="f1a08-127">Rozložení definuje šablonu nejvyšší úrovně pro zobrazení v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f1a08-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="f1a08-128">Aplikace nevyžadují rozložení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-128">Apps don't require a layout.</span></span> <span data-ttu-id="f1a08-129">Aplikace můžete definovat více než jedno rozložení s různá zobrazení zadání různá rozložení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="f1a08-130">Následující kód ukazuje soubor rozložení pro vytvoření projektu s kontroler a zobrazení šablony:</span><span class="sxs-lookup"><span data-stu-id="f1a08-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="f1a08-131">Určení rozložení</span><span class="sxs-lookup"><span data-stu-id="f1a08-131">Specifying a Layout</span></span>

<span data-ttu-id="f1a08-132">Zobrazení Razor je k dispozici `Layout` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f1a08-132">Razor views have a `Layout` property.</span></span> <span data-ttu-id="f1a08-133">Jednotlivá zobrazení zadat rozložení tak, že nastavíte tuto vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f1a08-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="f1a08-134">Zadané rozložení můžete použít úplnou cestu (například */Pages/Shared/_Layout.cshtml* nebo */Views/Shared/_Layout.cshtml*) nebo částečný název (Příklad: `_Layout`).</span><span class="sxs-lookup"><span data-stu-id="f1a08-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml*) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="f1a08-135">Když částečný název zadán, vyhledá zobrazovací modul Razor rozložení souboru pomocí jeho procesu zjišťování standardní.</span><span class="sxs-lookup"><span data-stu-id="f1a08-135">When a partial name is provided, the Razor view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="f1a08-136">Složky, pokud existuje metoda obslužné rutiny (nebo řadič) je nejprve prohledán, za nímž následuje *Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="f1a08-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="f1a08-137">Tento proces zjišťování je stejný jako proces používá ke zjišťování [částečná zobrazení](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="f1a08-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="f1a08-138">Ve výchozím nastavení, musí volat každou rozložení `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="f1a08-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="f1a08-139">Všude, kde volání `RenderBody` je umístěn, bude vykreslen obsah zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>

### <a name="sections"></a><span data-ttu-id="f1a08-140">Oddíly</span><span class="sxs-lookup"><span data-stu-id="f1a08-140">Sections</span></span>

<span data-ttu-id="f1a08-141">Rozložení můžete volitelně odkazovat na jeden nebo více *oddíly*, voláním `RenderSection`.</span><span class="sxs-lookup"><span data-stu-id="f1a08-141">A layout can optionally reference one or more *sections*, by calling `RenderSection`.</span></span> <span data-ttu-id="f1a08-142">Oddíly umožňují organizovat umístění některých prvků stránky.</span><span class="sxs-lookup"><span data-stu-id="f1a08-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="f1a08-143">Každé volání `RenderSection` můžete určit, zda je tento oddíl požadované nebo volitelné:</span><span class="sxs-lookup"><span data-stu-id="f1a08-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

<span data-ttu-id="f1a08-144">Pokud není nalezen požadovaný oddíl, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="f1a08-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="f1a08-145">Jednotlivá zobrazení zadejte obsah, který mohl být vykreslen v rámci oddílu pomocí `@section` syntaxi Razor.</span><span class="sxs-lookup"><span data-stu-id="f1a08-145">Individual views specify the content to be rendered within a section using the `@section` Razor syntax.</span></span> <span data-ttu-id="f1a08-146">Pokud na stránce nebo zobrazení určující sekci, je nutné vykreslit (nebo dojde k chybě).</span><span class="sxs-lookup"><span data-stu-id="f1a08-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="f1a08-147">Příklad `@section` definice v zobrazení pro stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="f1a08-147">An example `@section` definition in Razor Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

<span data-ttu-id="f1a08-148">V předchozím kódu *scripts/main.js* se přidá do `scripts` části na stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="f1a08-149">Další stránky nebo zobrazení ve stejné aplikaci nemusí potřebovat tento skript a nebude nadefinoval oddíl skripty.</span><span class="sxs-lookup"><span data-stu-id="f1a08-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="f1a08-150">Následující kód používá [pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) k vykreslení *_ValidationScriptsPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f1a08-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml*:</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="f1a08-151">Předchozí kód generovaný serverem [generování uživatelského rozhraní Identity](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="f1a08-151">The preceding markup was generated by [scaffolding Identity](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="f1a08-152">Oddíly definované v stránku nebo zobrazení jsou k dispozici pouze v její okamžitý rozložení stránky.</span><span class="sxs-lookup"><span data-stu-id="f1a08-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="f1a08-153">Nelze se odkazovat z částečných zobrazení, zobrazení komponenty nebo jiných částí systému zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="f1a08-154">Oddíly se ignoruje</span><span class="sxs-lookup"><span data-stu-id="f1a08-154">Ignoring sections</span></span>

<span data-ttu-id="f1a08-155">Ve výchozím nastavení text a všechny části na stránce obsahu musí všechny zobrazovat na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="f1a08-156">Zobrazovací modul Razor vynucuje Díky sledování, zda byl vykreslen text a každý oddíl.</span><span class="sxs-lookup"><span data-stu-id="f1a08-156">The Razor view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="f1a08-157">Chcete-li dát pokyn zobrazovací modul, aby ignoroval text nebo oddíly, zavolejte `IgnoreBody` a `IgnoreSection` metody.</span><span class="sxs-lookup"><span data-stu-id="f1a08-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="f1a08-158">Text a každý oddíl na stránce Razor musí být buď vykreslen nebo ignorovat.</span><span class="sxs-lookup"><span data-stu-id="f1a08-158">The body and every section in a Razor page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="f1a08-159">Import sdílených direktivy</span><span class="sxs-lookup"><span data-stu-id="f1a08-159">Importing Shared Directives</span></span>

<span data-ttu-id="f1a08-160">Zobrazení a stránky můžete použít direktivy Razor pro import obory názvů a použití [injektáž závislostí](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="f1a08-160">Views and pages can use Razor directives to importing namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="f1a08-161">Direktivy, které sdílí mnoho zobrazení je možné zadat běžný *_ViewImports.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="f1a08-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="f1a08-162">`_ViewImports` Soubor podporuje následující direktivy:</span><span class="sxs-lookup"><span data-stu-id="f1a08-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="f1a08-163">Tento soubor nepodporuje další funkce Razor, jako je například funkce a definice části.</span><span class="sxs-lookup"><span data-stu-id="f1a08-163">The file doesn't support other Razor features, such as functions and section definitions.</span></span>

<span data-ttu-id="f1a08-164">Ukázka `_ViewImports.cshtml` souboru:</span><span class="sxs-lookup"><span data-stu-id="f1a08-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="f1a08-165">*_ViewImports.cshtml* soubor pro aplikaci ASP.NET Core MVC je obvykle umístěn ve *stránky* (nebo *zobrazení*) složky.</span><span class="sxs-lookup"><span data-stu-id="f1a08-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="f1a08-166">A *_ViewImports.cshtml* souboru je možné použít v jakékoli složce, v takovém případě se použijí jenom u ke stránkám nebo zobrazení v této složce a jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="f1a08-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="f1a08-167">`_ViewImports` soubory se zpracovávají spouštění na kořenové úrovni a potom pro každou složku dovedou až k pozici stránky nebo zobrazení samotný.</span><span class="sxs-lookup"><span data-stu-id="f1a08-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="f1a08-168">`_ViewImports` bylo nastaveno na kořenové úrovni může přepsat na úrovni složek.</span><span class="sxs-lookup"><span data-stu-id="f1a08-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="f1a08-169">Například předpokládejme, že:</span><span class="sxs-lookup"><span data-stu-id="f1a08-169">For example, suppose:</span></span>

* <span data-ttu-id="f1a08-170">Kořenové úrovni *_ViewImports.cshtml* soubor obsahuje `@model MyModel1` a `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="f1a08-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="f1a08-171">Podsložky *_ViewImports.cshtml* soubor obsahuje `@model MyModel2` a `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="f1a08-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="f1a08-172">Stránky a zobrazení v podsložce bude mít přístup k oběma pomocných rutin značek a `MyModel2` modelu.</span><span class="sxs-lookup"><span data-stu-id="f1a08-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="f1a08-173">Pokud je položek víc *_ViewImports.cshtml* soubory se nacházejí v hierarchii souborů kombinované chování direktivy jsou:</span><span class="sxs-lookup"><span data-stu-id="f1a08-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="f1a08-174">`@addTagHelper`, `@removeTagHelper`: všech spuštění, v pořadí</span><span class="sxs-lookup"><span data-stu-id="f1a08-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="f1a08-175">`@tagHelperPrefix`: na ten nejbližší do zobrazení přepíše všechny ostatní</span><span class="sxs-lookup"><span data-stu-id="f1a08-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f1a08-176">`@model`: na ten nejbližší do zobrazení přepíše všechny ostatní</span><span class="sxs-lookup"><span data-stu-id="f1a08-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f1a08-177">`@inherits`: na ten nejbližší do zobrazení přepíše všechny ostatní</span><span class="sxs-lookup"><span data-stu-id="f1a08-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="f1a08-178">`@using`: všechny jsou zahrnuty; duplicity se ignorují.</span><span class="sxs-lookup"><span data-stu-id="f1a08-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="f1a08-179">`@inject`: pro každou vlastnost na ten nejbližší do zobrazení přepíše všechny ostatní se stejným názvem vlastnosti</span><span class="sxs-lookup"><span data-stu-id="f1a08-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="f1a08-180">Spuštění kódu před každou zobrazení</span><span class="sxs-lookup"><span data-stu-id="f1a08-180">Running Code Before Each View</span></span>

<span data-ttu-id="f1a08-181">Kód, který je potřeba spustit před každou zobrazení nebo stránky musí být umístěné ve *soubor _ViewStart.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="f1a08-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="f1a08-182">Podle konvence *soubor _ViewStart.cshtml* soubor se nachází v *stránky* (nebo *zobrazení*) složky.</span><span class="sxs-lookup"><span data-stu-id="f1a08-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views*) folder.</span></span> <span data-ttu-id="f1a08-183">Příkazy uvedené v *soubor _ViewStart.cshtml* jsou spouštěny před každou úplné zobrazení (nikoli rozložení a není částečná zobrazení).</span><span class="sxs-lookup"><span data-stu-id="f1a08-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="f1a08-184">Stejně jako [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *soubor _ViewStart.cshtml* jsou hierarchická.</span><span class="sxs-lookup"><span data-stu-id="f1a08-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="f1a08-185">Pokud *soubor _ViewStart.cshtml* soubor je definován ve složce zobrazení nebo stránky se spustí po definovanému v kořenovém adresáři *stránky* (nebo *zobrazení*) složku (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="f1a08-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views*) folder (if any).</span></span>

<span data-ttu-id="f1a08-186">Ukázka *soubor _ViewStart.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="f1a08-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="f1a08-187">Výše uvedeného souboru Určuje, zda budou používat všechna zobrazení *_Layout.cshtml* rozložení.</span><span class="sxs-lookup"><span data-stu-id="f1a08-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="f1a08-188">*Soubor _ViewStart.cshtml* a *_ViewImports.cshtml* jsou **není** obvykle umístěné v */stránek/Shared* (nebo   */zobrazení/Shared*) složka.</span><span class="sxs-lookup"><span data-stu-id="f1a08-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared*) folder.</span></span> <span data-ttu-id="f1a08-189">Verze těchto souborů úrovni aplikace by měl být umístěn v přímo */stránky* (nebo */zobrazení*) složky.</span><span class="sxs-lookup"><span data-stu-id="f1a08-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views*) folder.</span></span>
