---
title: Oblasti v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak jsou oblasti ASP.NET funkce MVC používaná k uspořádání souvisejících funkcí do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení).
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625933"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="6f47c-103">Oblasti v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="6f47c-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="6f47c-104">Podle [Dhananjay Kumar](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6f47c-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6f47c-105">Oblasti jsou ASP.NET funkce používaná k uspořádání souvisejících funkcí do skupiny jako samostatná funkce:</span><span class="sxs-lookup"><span data-stu-id="6f47c-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="6f47c-106">Obor názvů pro směrování.</span><span class="sxs-lookup"><span data-stu-id="6f47c-106">Namespace for routing.</span></span>
* <span data-ttu-id="6f47c-107">Struktura složek pro zobrazení a holicí strojek stránky.</span><span class="sxs-lookup"><span data-stu-id="6f47c-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="6f47c-108">Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` dalšího `action` parametru `page`trasy , `area`na stránku razor nebo .</span><span class="sxs-lookup"><span data-stu-id="6f47c-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="6f47c-109">Oblasti poskytují způsob, jak rozdělit ASP.NET základní webové aplikace do menších funkčních skupin, z nichž každá má vlastní sadu razor stránky, ovladače, zobrazení a modely.</span><span class="sxs-lookup"><span data-stu-id="6f47c-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="6f47c-110">Oblast je efektivně struktura uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="6f47c-111">Ve webovém projektu ASP.NET Core jsou logické součásti, jako jsou stránky, model, řadič a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="6f47c-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="6f47c-112">ASP.NET Core runtime používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="6f47c-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="6f47c-113">Pro velkou aplikaci může být výhodné rozdělit aplikaci do samostatných oblastí funkcí na vysoké úrovni.</span><span class="sxs-lookup"><span data-stu-id="6f47c-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="6f47c-114">Například aplikace pro elektronické obchodování s více organizačními jednotkami, jako je pokladna, fakturace a vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="6f47c-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="6f47c-115">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, ovladače, stránky Razor a modely.</span><span class="sxs-lookup"><span data-stu-id="6f47c-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="6f47c-116">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="6f47c-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="6f47c-117">Aplikace je tvořena více funkčními součástmi na vysoké úrovni, které lze logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="6f47c-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="6f47c-118">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="6f47c-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="6f47c-119">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6f47c-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="6f47c-120">Ukázka stahování poskytuje základní aplikaci pro testovací oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="6f47c-121">Pokud používáte Razor Pages, přečtěte si [téma Oblasti se stránkami s břitvou](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6f47c-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="6f47c-122">Oblasti pro regulátory se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="6f47c-122">Areas for controllers with views</span></span>

<span data-ttu-id="6f47c-123">Typická webová aplikace ASP.NET Core používající oblasti, ovladače a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="6f47c-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="6f47c-124">[Struktura složek oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="6f47c-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="6f47c-125">Řadiče s [`[Area]`](#attribute) atributem přidružit řadič s oblastí:</span><span class="sxs-lookup"><span data-stu-id="6f47c-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="6f47c-126">[Oblastní trasa přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="6f47c-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="6f47c-127">Struktura složek oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-127">Area folder structure</span></span>

<span data-ttu-id="6f47c-128">Zvažte aplikaci, která má dvě logické *skupiny, produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="6f47c-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="6f47c-129">Pomocí oblastí by struktura složek byla podobná následujícímu:</span><span class="sxs-lookup"><span data-stu-id="6f47c-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="6f47c-130">Název projektu</span><span class="sxs-lookup"><span data-stu-id="6f47c-130">Project name</span></span>
  * <span data-ttu-id="6f47c-131">Oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-131">Areas</span></span>
    * <span data-ttu-id="6f47c-132">Produkty</span><span class="sxs-lookup"><span data-stu-id="6f47c-132">Products</span></span>
      * <span data-ttu-id="6f47c-133">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="6f47c-133">Controllers</span></span>
        * <span data-ttu-id="6f47c-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="6f47c-134">HomeController.cs</span></span>
        * <span data-ttu-id="6f47c-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="6f47c-135">ManageController.cs</span></span>
      * <span data-ttu-id="6f47c-136">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="6f47c-136">Views</span></span>
        * <span data-ttu-id="6f47c-137">Domů</span><span class="sxs-lookup"><span data-stu-id="6f47c-137">Home</span></span>
          * <span data-ttu-id="6f47c-138">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-138">Index.cshtml</span></span>
        * <span data-ttu-id="6f47c-139">Spravovat</span><span class="sxs-lookup"><span data-stu-id="6f47c-139">Manage</span></span>
          * <span data-ttu-id="6f47c-140">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-140">Index.cshtml</span></span>
          * <span data-ttu-id="6f47c-141">O souboru O.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-141">About.cshtml</span></span>
    * <span data-ttu-id="6f47c-142">Služby</span><span class="sxs-lookup"><span data-stu-id="6f47c-142">Services</span></span>
      * <span data-ttu-id="6f47c-143">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="6f47c-143">Controllers</span></span>
        * <span data-ttu-id="6f47c-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="6f47c-144">HomeController.cs</span></span>
      * <span data-ttu-id="6f47c-145">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="6f47c-145">Views</span></span>
        * <span data-ttu-id="6f47c-146">Domů</span><span class="sxs-lookup"><span data-stu-id="6f47c-146">Home</span></span>
          * <span data-ttu-id="6f47c-147">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-147">Index.cshtml</span></span>

<span data-ttu-id="6f47c-148">Zatímco předchozí rozložení je typické při použití oblasti, pouze soubory zobrazení jsou nutné použít tuto strukturu složek.</span><span class="sxs-lookup"><span data-stu-id="6f47c-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="6f47c-149">Zobrazení hledání hledání odpovídajícího souboru zobrazení oblasti v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="6f47c-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="6f47c-150">Přidružení řadiče k oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-150">Associate the controller with an Area</span></span>

<span data-ttu-id="6f47c-151">Oblastní kontroloři jsou označeni atributem [ &lbrack;Area:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)</span><span class="sxs-lookup"><span data-stu-id="6f47c-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="6f47c-152">Přidat oblast trasy</span><span class="sxs-lookup"><span data-stu-id="6f47c-152">Add Area route</span></span>

<span data-ttu-id="6f47c-153">Trasy oblasti obvykle používají [konvenční směrování](xref:mvc/controllers/routing#cr) spíše než [směrování atributů](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="6f47c-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="6f47c-154">Konvenční směrování je závislé na objednávce.</span><span class="sxs-lookup"><span data-stu-id="6f47c-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="6f47c-155">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce tras, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="6f47c-156">`{area:...}`lze použít jako token v šablonách tras, pokud je místo url jednotné ve všech oblastech:</span><span class="sxs-lookup"><span data-stu-id="6f47c-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="6f47c-157">V předchozím kódu `exists` použije omezení, že trasa musí odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="6f47c-158">Použití `{area:...}` `MapControllerRoute`s :</span><span class="sxs-lookup"><span data-stu-id="6f47c-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="6f47c-159">Je nejméně složitý mechanismus přidání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="6f47c-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="6f47c-160">Porovná všechny řadiče `[Area("Area name")]` s atributem.</span><span class="sxs-lookup"><span data-stu-id="6f47c-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="6f47c-161">Následující kód <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> používá k vytvoření dvou pojmenovaných tras oblasti:</span><span class="sxs-lookup"><span data-stu-id="6f47c-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="6f47c-162">Další informace naleznete v tématu [Area routing](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="6f47c-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="6f47c-163">Propojení generování s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="6f47c-163">Link generation with MVC areas</span></span>

<span data-ttu-id="6f47c-164">Následující kód z [ukázkové stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ukazuje generování propojení se zadanou oblastí:</span><span class="sxs-lookup"><span data-stu-id="6f47c-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="6f47c-165">Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="6f47c-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="6f47c-166">Předchozí odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-166">The preceding links.</span></span>
* <span data-ttu-id="6f47c-167">Odkazy podobné předchozí except `area` není zadán.</span><span class="sxs-lookup"><span data-stu-id="6f47c-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="6f47c-168">Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="6f47c-169">Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti a kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6f47c-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="6f47c-170">Pokud není zadána oblast nebo řadič, směrování závisí na [okolních](xref:mvc/controllers/routing#ambient) hodnotách.</span><span class="sxs-lookup"><span data-stu-id="6f47c-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="6f47c-171">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení.</span><span class="sxs-lookup"><span data-stu-id="6f47c-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="6f47c-172">V mnoha případech pro ukázkovou aplikaci, pomocí okolníhodnoty generuje nesprávné odkazy se značkami, které neurčuje oblast.</span><span class="sxs-lookup"><span data-stu-id="6f47c-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="6f47c-173">Další informace naleznete v [tématu Směrování k akcím řadiče](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="6f47c-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="6f47c-174">Sdílené rozložení pro oblasti používající soubor _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="6f47c-175">Chcete-li sdílet společné rozložení pro celou aplikaci, ponechejte *_ViewStart.cshtml* v [kořenové složce aplikace](#arf).</span><span class="sxs-lookup"><span data-stu-id="6f47c-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="6f47c-176">Další informace najdete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="6f47c-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="6f47c-177">Kořenová složka aplikace</span><span class="sxs-lookup"><span data-stu-id="6f47c-177">Application root folder</span></span>

<span data-ttu-id="6f47c-178">Kořenová složka aplikace je složka obsahující *Startup.cs* ve webové aplikaci vytvořené pomocí šablon ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6f47c-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="6f47c-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="6f47c-180">*/Views/_ViewImports.cshtml*, pro MVC a */Pages/_ViewImports.cshtml* pro Razor Pages, se neimportuje do zobrazení v oblastech.</span><span class="sxs-lookup"><span data-stu-id="6f47c-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="6f47c-181">Pomocí jednoho z následujících přístupů můžete zajistit import zobrazení do všech zobrazení:</span><span class="sxs-lookup"><span data-stu-id="6f47c-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="6f47c-182">Přidejte *_ViewImports.cshtml* do [kořenové složky aplikace](#arf).</span><span class="sxs-lookup"><span data-stu-id="6f47c-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="6f47c-183">Pro všechna zobrazení v aplikaci se bude vztahovat *_ViewImports.cshtml* v kořenové složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="6f47c-184">Zkopírujte soubor *_ViewImports.cshtml* do příslušné složky zobrazení pod oblastmi.</span><span class="sxs-lookup"><span data-stu-id="6f47c-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="6f47c-185">Soubor *_ViewImports.cshtml* obvykle obsahuje tag [helpery](xref:mvc/views/tag-helpers/intro) importy `@using`a `@inject` příkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="6f47c-186">Další informace naleznete [v tématu Import sdílených směrnic](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="6f47c-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="6f47c-187">Změna výchozí složky oblastí, ve které jsou uložena zobrazení</span><span class="sxs-lookup"><span data-stu-id="6f47c-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="6f47c-188">Následující kód změní výchozí složku `"Areas"` `"MyAreas"`oblastí z do :</span><span class="sxs-lookup"><span data-stu-id="6f47c-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="6f47c-189">Oblasti s břitvou stránky</span><span class="sxs-lookup"><span data-stu-id="6f47c-189">Areas with Razor Pages</span></span>

<span data-ttu-id="6f47c-190">Oblasti s Razor `Areas/<area name>/Pages` Pages vyžadují složku v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="6f47c-191">Následující struktura složek se používá s [ukázkovou aplikací](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="6f47c-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="6f47c-192">Název projektu</span><span class="sxs-lookup"><span data-stu-id="6f47c-192">Project name</span></span>
  * <span data-ttu-id="6f47c-193">Oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-193">Areas</span></span>
    * <span data-ttu-id="6f47c-194">Produkty</span><span class="sxs-lookup"><span data-stu-id="6f47c-194">Products</span></span>
      * <span data-ttu-id="6f47c-195">Stránky</span><span class="sxs-lookup"><span data-stu-id="6f47c-195">Pages</span></span>
        * <span data-ttu-id="6f47c-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="6f47c-196">_ViewImports</span></span>
        * <span data-ttu-id="6f47c-197">Informace</span><span class="sxs-lookup"><span data-stu-id="6f47c-197">About</span></span>
        * <span data-ttu-id="6f47c-198">Index</span><span class="sxs-lookup"><span data-stu-id="6f47c-198">Index</span></span>
    * <span data-ttu-id="6f47c-199">Služby</span><span class="sxs-lookup"><span data-stu-id="6f47c-199">Services</span></span>
      * <span data-ttu-id="6f47c-200">Stránky</span><span class="sxs-lookup"><span data-stu-id="6f47c-200">Pages</span></span>
        * <span data-ttu-id="6f47c-201">Spravovat</span><span class="sxs-lookup"><span data-stu-id="6f47c-201">Manage</span></span>
          * <span data-ttu-id="6f47c-202">Informace</span><span class="sxs-lookup"><span data-stu-id="6f47c-202">About</span></span>
          * <span data-ttu-id="6f47c-203">Index</span><span class="sxs-lookup"><span data-stu-id="6f47c-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="6f47c-204">Propojení generování se stránkami a oblastmi Razor</span><span class="sxs-lookup"><span data-stu-id="6f47c-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="6f47c-205">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) ukazuje generování propojení se `asp-area="Products"`zadanou oblastí (například ):</span><span class="sxs-lookup"><span data-stu-id="6f47c-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="6f47c-206">Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="6f47c-207">Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="6f47c-208">Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="6f47c-209">Pokud oblast není zadána, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="6f47c-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="6f47c-210">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení.</span><span class="sxs-lookup"><span data-stu-id="6f47c-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="6f47c-211">V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="6f47c-212">Zvažte například odkazy generované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="6f47c-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="6f47c-213">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="6f47c-213">For the preceding code:</span></span>

* <span data-ttu-id="6f47c-214">Odkaz generovaný `<a asp-page="/Manage/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `Services` oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="6f47c-215">Například `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`nebo .</span><span class="sxs-lookup"><span data-stu-id="6f47c-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="6f47c-216">Odkaz generovaný `<a asp-page="/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `/Home`.</span><span class="sxs-lookup"><span data-stu-id="6f47c-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="6f47c-217">Kód je z [ukázky ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="6f47c-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="6f47c-218">Import oboru názvů a pomocníků značek pomocí _ViewImports souboru</span><span class="sxs-lookup"><span data-stu-id="6f47c-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="6f47c-219">Do každé složky *Stránky* s adresou domény a pomocníků značek lze přidat soubor *_ViewImports.cshtml* a importovat tak obor názvů a pomocníky značek do každé stránky Razor ve složce.</span><span class="sxs-lookup"><span data-stu-id="6f47c-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="6f47c-220">Zvažte oblast *Služby* ukázkového kódu, která neobsahuje soubor *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6f47c-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="6f47c-221">Následující značky ukazují *stránku /Services/Manage/About* Razor:</span><span class="sxs-lookup"><span data-stu-id="6f47c-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="6f47c-222">V předchozí chrupu:</span><span class="sxs-lookup"><span data-stu-id="6f47c-222">In the preceding markup:</span></span>

* <span data-ttu-id="6f47c-223">K určení modelu`@model RPareas.Areas.Services.Pages.Manage.AboutModel`( ).</span><span class="sxs-lookup"><span data-stu-id="6f47c-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="6f47c-224">[Pomocné spoje](xref:mvc/views/tag-helpers/intro) tagů jsou povoleny`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="6f47c-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="6f47c-225">V ukázkovém stažení obsahuje oblast Produkty následující soubor *_ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6f47c-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="6f47c-226">Následující značky ukazují */Products/About* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="6f47c-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="6f47c-227">V předchozím souboru je obor `@addTagHelper` názvů a směrnice importovány do souboru souborem *Oblasti/Produkty/Stránky/_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6f47c-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="6f47c-228">Další informace naleznete [v tématu Správa oboru pomocné spoje značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a import [sdílených směrnic](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="6f47c-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="6f47c-229">Sdílené rozložení pro oblasti břitvy stránek</span><span class="sxs-lookup"><span data-stu-id="6f47c-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="6f47c-230">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart.cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="6f47c-231">Publikační oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-231">Publishing Areas</span></span>

<span data-ttu-id="6f47c-232">Všechny soubory \*.cshtml a soubory v adresáři *wwwroot* jsou publikovány do výstupu, pokud `<Project Sdk="Microsoft.NET.Sdk.Web">` jsou zahrnuty do souboru \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="6f47c-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6f47c-233">Oblasti jsou ASP.NET funkce používaná k uspořádání souvisejících funkcí do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="6f47c-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="6f47c-234">Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` dalšího `action` parametru `page`trasy , `area`na stránku razor nebo .</span><span class="sxs-lookup"><span data-stu-id="6f47c-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="6f47c-235">Oblasti poskytují způsob, jak rozdělit ASP.NET základní webové aplikace do menších funkčních skupin, z nichž každá má vlastní sadu razor stránky, ovladače, zobrazení a modely.</span><span class="sxs-lookup"><span data-stu-id="6f47c-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="6f47c-236">Oblast je efektivně struktura uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="6f47c-237">Ve webovém projektu ASP.NET Core jsou logické součásti, jako jsou stránky, model, řadič a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="6f47c-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="6f47c-238">ASP.NET Core runtime používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="6f47c-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="6f47c-239">Pro velkou aplikaci může být výhodné rozdělit aplikaci do samostatných oblastí funkcí na vysoké úrovni.</span><span class="sxs-lookup"><span data-stu-id="6f47c-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="6f47c-240">Například aplikace pro elektronické obchodování s více organizačními jednotkami, jako je pokladna, fakturace a vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="6f47c-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="6f47c-241">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, ovladače, stránky Razor a modely.</span><span class="sxs-lookup"><span data-stu-id="6f47c-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="6f47c-242">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="6f47c-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="6f47c-243">Aplikace je tvořena více funkčními součástmi na vysoké úrovni, které lze logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="6f47c-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="6f47c-244">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="6f47c-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="6f47c-245">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6f47c-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="6f47c-246">Ukázka stahování poskytuje základní aplikaci pro testovací oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="6f47c-247">Pokud používáte Razor Pages, přečtěte si [téma Oblasti se stránkami s břitvou](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6f47c-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="6f47c-248">Oblasti pro regulátory se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="6f47c-248">Areas for controllers with views</span></span>

<span data-ttu-id="6f47c-249">Typická webová aplikace ASP.NET Core používající oblasti, ovladače a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="6f47c-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="6f47c-250">[Struktura složek oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="6f47c-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="6f47c-251">Řadiče s [`[Area]`](#attribute) atributem přidružit řadič s oblastí:</span><span class="sxs-lookup"><span data-stu-id="6f47c-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="6f47c-252">[Oblastní trasa přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="6f47c-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="6f47c-253">Struktura složek oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-253">Area folder structure</span></span>

<span data-ttu-id="6f47c-254">Zvažte aplikaci, která má dvě logické *skupiny, produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="6f47c-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="6f47c-255">Pomocí oblastí by struktura složek byla podobná následujícímu:</span><span class="sxs-lookup"><span data-stu-id="6f47c-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="6f47c-256">Název projektu</span><span class="sxs-lookup"><span data-stu-id="6f47c-256">Project name</span></span>
  * <span data-ttu-id="6f47c-257">Oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-257">Areas</span></span>
    * <span data-ttu-id="6f47c-258">Produkty</span><span class="sxs-lookup"><span data-stu-id="6f47c-258">Products</span></span>
      * <span data-ttu-id="6f47c-259">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="6f47c-259">Controllers</span></span>
        * <span data-ttu-id="6f47c-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="6f47c-260">HomeController.cs</span></span>
        * <span data-ttu-id="6f47c-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="6f47c-261">ManageController.cs</span></span>
      * <span data-ttu-id="6f47c-262">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="6f47c-262">Views</span></span>
        * <span data-ttu-id="6f47c-263">Domů</span><span class="sxs-lookup"><span data-stu-id="6f47c-263">Home</span></span>
          * <span data-ttu-id="6f47c-264">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-264">Index.cshtml</span></span>
        * <span data-ttu-id="6f47c-265">Spravovat</span><span class="sxs-lookup"><span data-stu-id="6f47c-265">Manage</span></span>
          * <span data-ttu-id="6f47c-266">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-266">Index.cshtml</span></span>
          * <span data-ttu-id="6f47c-267">O souboru O.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-267">About.cshtml</span></span>
    * <span data-ttu-id="6f47c-268">Služby</span><span class="sxs-lookup"><span data-stu-id="6f47c-268">Services</span></span>
      * <span data-ttu-id="6f47c-269">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="6f47c-269">Controllers</span></span>
        * <span data-ttu-id="6f47c-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="6f47c-270">HomeController.cs</span></span>
      * <span data-ttu-id="6f47c-271">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="6f47c-271">Views</span></span>
        * <span data-ttu-id="6f47c-272">Domů</span><span class="sxs-lookup"><span data-stu-id="6f47c-272">Home</span></span>
          * <span data-ttu-id="6f47c-273">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-273">Index.cshtml</span></span>

<span data-ttu-id="6f47c-274">Zatímco předchozí rozložení je typické při použití oblasti, pouze soubory zobrazení jsou nutné použít tuto strukturu složek.</span><span class="sxs-lookup"><span data-stu-id="6f47c-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="6f47c-275">Zobrazení hledání hledání odpovídajícího souboru zobrazení oblasti v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="6f47c-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="6f47c-276">Přidružení řadiče k oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-276">Associate the controller with an Area</span></span>

<span data-ttu-id="6f47c-277">Oblastní kontroloři jsou označeni atributem [ &lbrack;Area:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)</span><span class="sxs-lookup"><span data-stu-id="6f47c-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="6f47c-278">Přidat oblast trasy</span><span class="sxs-lookup"><span data-stu-id="6f47c-278">Add Area route</span></span>

<span data-ttu-id="6f47c-279">Trasy oblasti obvykle používají konvenční směrování spíše než směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="6f47c-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="6f47c-280">Konvenční směrování je závislé na objednávce.</span><span class="sxs-lookup"><span data-stu-id="6f47c-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="6f47c-281">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce tras, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="6f47c-282">`{area:...}`lze použít jako token v šablonách tras, pokud je místo url jednotné ve všech oblastech:</span><span class="sxs-lookup"><span data-stu-id="6f47c-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="6f47c-283">V předchozím kódu `exists` použije omezení, že trasa musí odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="6f47c-284">Použití `{area:...}` je nejméně složitý mechanismus přidání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="6f47c-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="6f47c-285">Následující kód <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> používá k vytvoření dvou pojmenovaných tras oblasti:</span><span class="sxs-lookup"><span data-stu-id="6f47c-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="6f47c-286">Při `MapAreaRoute` použití s ASP.NET Core 2.2, podívejte se na [tento problém GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="6f47c-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="6f47c-287">Další informace naleznete v tématu [Area routing](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="6f47c-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="6f47c-288">Propojení generování s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="6f47c-288">Link generation with MVC areas</span></span>

<span data-ttu-id="6f47c-289">Následující kód z [ukázkové stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ukazuje generování propojení se zadanou oblastí:</span><span class="sxs-lookup"><span data-stu-id="6f47c-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="6f47c-290">Odkazy generované s předchozím kódem jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6f47c-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="6f47c-291">Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="6f47c-292">Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="6f47c-293">Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti a kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6f47c-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="6f47c-294">Pokud není zadána oblast nebo řadič, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="6f47c-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="6f47c-295">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení.</span><span class="sxs-lookup"><span data-stu-id="6f47c-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="6f47c-296">V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="6f47c-297">Další informace naleznete v [tématu Směrování k akcím řadiče](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="6f47c-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="6f47c-298">Sdílené rozložení pro oblasti používající soubor _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="6f47c-299">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart.cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="6f47c-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="6f47c-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="6f47c-301">Ve svém standardním umístění */Views/_ViewImports.cshtml* se nevztahuje na oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="6f47c-302">Chcete-li používat běžné [pomocné složky značek](xref:mvc/views/tag-helpers/intro) `@using`, nebo `@inject` ve vaší oblasti, zajistěte, aby [se pro zobrazení oblasti vztahoval](xref:mvc/views/layout#importing-shared-directives)správný soubor *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="6f47c-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="6f47c-303">Pokud chcete stejné chování ve všech zobrazeních, přesuňte */Views/_ViewImports.cshtml* do kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="6f47c-304">Změna výchozí složky oblastí, ve které jsou uložena zobrazení</span><span class="sxs-lookup"><span data-stu-id="6f47c-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="6f47c-305">Následující kód změní výchozí složku `"Areas"` `"MyAreas"`oblastí z do :</span><span class="sxs-lookup"><span data-stu-id="6f47c-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="6f47c-306">Oblasti s břitvou stránky</span><span class="sxs-lookup"><span data-stu-id="6f47c-306">Areas with Razor Pages</span></span>

<span data-ttu-id="6f47c-307">Oblasti s Razor `Areas/<area name>/Pages` Pages vyžadují složku v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="6f47c-308">Následující struktura složek se používá s [ukázkovou aplikací](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="6f47c-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="6f47c-309">Název projektu</span><span class="sxs-lookup"><span data-stu-id="6f47c-309">Project name</span></span>
  * <span data-ttu-id="6f47c-310">Oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-310">Areas</span></span>
    * <span data-ttu-id="6f47c-311">Produkty</span><span class="sxs-lookup"><span data-stu-id="6f47c-311">Products</span></span>
      * <span data-ttu-id="6f47c-312">Stránky</span><span class="sxs-lookup"><span data-stu-id="6f47c-312">Pages</span></span>
        * <span data-ttu-id="6f47c-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="6f47c-313">_ViewImports</span></span>
        * <span data-ttu-id="6f47c-314">Informace</span><span class="sxs-lookup"><span data-stu-id="6f47c-314">About</span></span>
        * <span data-ttu-id="6f47c-315">Index</span><span class="sxs-lookup"><span data-stu-id="6f47c-315">Index</span></span>
    * <span data-ttu-id="6f47c-316">Služby</span><span class="sxs-lookup"><span data-stu-id="6f47c-316">Services</span></span>
      * <span data-ttu-id="6f47c-317">Stránky</span><span class="sxs-lookup"><span data-stu-id="6f47c-317">Pages</span></span>
        * <span data-ttu-id="6f47c-318">Spravovat</span><span class="sxs-lookup"><span data-stu-id="6f47c-318">Manage</span></span>
          * <span data-ttu-id="6f47c-319">Informace</span><span class="sxs-lookup"><span data-stu-id="6f47c-319">About</span></span>
          * <span data-ttu-id="6f47c-320">Index</span><span class="sxs-lookup"><span data-stu-id="6f47c-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="6f47c-321">Propojení generování se stránkami a oblastmi Razor</span><span class="sxs-lookup"><span data-stu-id="6f47c-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="6f47c-322">Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) ukazuje generování propojení se `asp-area="Products"`zadanou oblastí (například ):</span><span class="sxs-lookup"><span data-stu-id="6f47c-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="6f47c-323">Odkazy generované s předchozím kódem jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6f47c-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="6f47c-324">Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="6f47c-325">Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="6f47c-326">Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="6f47c-327">Pokud oblast není zadána, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="6f47c-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="6f47c-328">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení.</span><span class="sxs-lookup"><span data-stu-id="6f47c-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="6f47c-329">V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="6f47c-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="6f47c-330">Zvažte například odkazy generované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="6f47c-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="6f47c-331">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="6f47c-331">For the preceding code:</span></span>

* <span data-ttu-id="6f47c-332">Odkaz generovaný `<a asp-page="/Manage/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `Services` oblasti.</span><span class="sxs-lookup"><span data-stu-id="6f47c-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="6f47c-333">Například `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`nebo .</span><span class="sxs-lookup"><span data-stu-id="6f47c-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="6f47c-334">Odkaz generovaný `<a asp-page="/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `/Home`.</span><span class="sxs-lookup"><span data-stu-id="6f47c-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="6f47c-335">Kód je z [ukázky ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="6f47c-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="6f47c-336">Import oboru názvů a pomocníků značek pomocí _ViewImports souboru</span><span class="sxs-lookup"><span data-stu-id="6f47c-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="6f47c-337">Do každé složky *Stránky* s adresou domény a pomocníků značek lze přidat soubor *_ViewImports.cshtml* a importovat tak obor názvů a pomocníky značek do každé stránky Razor ve složce.</span><span class="sxs-lookup"><span data-stu-id="6f47c-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="6f47c-338">Zvažte oblast *Služby* ukázkového kódu, která neobsahuje soubor *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6f47c-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="6f47c-339">Následující značky ukazují *stránku /Services/Manage/About* Razor:</span><span class="sxs-lookup"><span data-stu-id="6f47c-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="6f47c-340">V předchozí chrupu:</span><span class="sxs-lookup"><span data-stu-id="6f47c-340">In the preceding markup:</span></span>

* <span data-ttu-id="6f47c-341">K určení modelu`@model RPareas.Areas.Services.Pages.Manage.AboutModel`( ).</span><span class="sxs-lookup"><span data-stu-id="6f47c-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="6f47c-342">[Pomocné spoje](xref:mvc/views/tag-helpers/intro) tagů jsou povoleny`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="6f47c-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="6f47c-343">V ukázkovém stažení obsahuje oblast Produkty následující soubor *_ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6f47c-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="6f47c-344">Následující značky ukazují */Products/About* Razor Page:</span><span class="sxs-lookup"><span data-stu-id="6f47c-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="6f47c-345">V předchozím souboru je obor `@addTagHelper` názvů a směrnice importovány do souboru souborem *Oblasti/Produkty/Stránky/_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6f47c-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="6f47c-346">Další informace naleznete [v tématu Správa oboru pomocné spoje značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a import [sdílených směrnic](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="6f47c-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="6f47c-347">Sdílené rozložení pro oblasti břitvy stránek</span><span class="sxs-lookup"><span data-stu-id="6f47c-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="6f47c-348">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart.cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="6f47c-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="6f47c-349">Publikační oblasti</span><span class="sxs-lookup"><span data-stu-id="6f47c-349">Publishing Areas</span></span>

<span data-ttu-id="6f47c-350">Všechny soubory \*.cshtml a soubory v adresáři *wwwroot* jsou publikovány do výstupu, pokud `<Project Sdk="Microsoft.NET.Sdk.Web">` jsou zahrnuty do souboru \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="6f47c-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
