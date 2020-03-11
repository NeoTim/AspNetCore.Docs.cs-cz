---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se jedná o funkci ASP.NET MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 41f7bdd6dbb3e33f843cb2a765dd30f98c81ce21
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78665401"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="29dbe-103">Oblasti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29dbe-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="29dbe-104">Od [Dhananjay Kumarem](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="29dbe-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29dbe-105">Oblasti jsou funkce ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného:</span><span class="sxs-lookup"><span data-stu-id="29dbe-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="29dbe-106">Obor názvů pro směrování</span><span class="sxs-lookup"><span data-stu-id="29dbe-106">Namespace for routing.</span></span>
* <span data-ttu-id="29dbe-107">Struktura složek pro zobrazení a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="29dbe-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="29dbe-108">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy, `area`, `controller` a `action` nebo `page`stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="29dbe-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="29dbe-109">Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní sadu Razor Pages, řadičů, zobrazení a modelů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="29dbe-110">Oblast je ve skutečnosti strukturou uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="29dbe-111">Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="29dbe-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="29dbe-112">Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="29dbe-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="29dbe-113">Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně.</span><span class="sxs-lookup"><span data-stu-id="29dbe-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="29dbe-114">Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="29dbe-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="29dbe-115">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče, Razor Pages a modely.</span><span class="sxs-lookup"><span data-stu-id="29dbe-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="29dbe-116">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="29dbe-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="29dbe-117">Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="29dbe-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="29dbe-118">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="29dbe-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="29dbe-119">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="29dbe-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="29dbe-120">Ukázka stažení poskytuje základní aplikaci pro oblasti testování.</span><span class="sxs-lookup"><span data-stu-id="29dbe-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="29dbe-121">Pokud používáte Razor Pages, přečtěte si téma [oblasti s Razor Pages](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="29dbe-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="29dbe-122">Oblasti pro řadiče se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="29dbe-122">Areas for controllers with views</span></span>

<span data-ttu-id="29dbe-123">Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="29dbe-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="29dbe-124">[Struktura složky oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="29dbe-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="29dbe-125">Řadiče s atributem [`[Area]`](#attribute) k přidružení kontroleru k oblasti:</span><span class="sxs-lookup"><span data-stu-id="29dbe-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="29dbe-126">[Trasa oblasti přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="29dbe-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="29dbe-127">Struktura složky oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-127">Area folder structure</span></span>

<span data-ttu-id="29dbe-128">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="29dbe-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="29dbe-129">Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="29dbe-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="29dbe-130">Název projektu</span><span class="sxs-lookup"><span data-stu-id="29dbe-130">Project name</span></span>
  * <span data-ttu-id="29dbe-131">Oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-131">Areas</span></span>
    * <span data-ttu-id="29dbe-132">Produkty</span><span class="sxs-lookup"><span data-stu-id="29dbe-132">Products</span></span>
      * <span data-ttu-id="29dbe-133">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="29dbe-133">Controllers</span></span>
        * <span data-ttu-id="29dbe-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="29dbe-134">HomeController.cs</span></span>
        * <span data-ttu-id="29dbe-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="29dbe-135">ManageController.cs</span></span>
      * <span data-ttu-id="29dbe-136">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="29dbe-136">Views</span></span>
        * <span data-ttu-id="29dbe-137">Domů</span><span class="sxs-lookup"><span data-stu-id="29dbe-137">Home</span></span>
          * <span data-ttu-id="29dbe-138">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-138">Index.cshtml</span></span>
        * <span data-ttu-id="29dbe-139">Správa</span><span class="sxs-lookup"><span data-stu-id="29dbe-139">Manage</span></span>
          * <span data-ttu-id="29dbe-140">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-140">Index.cshtml</span></span>
          * <span data-ttu-id="29dbe-141">O. cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-141">About.cshtml</span></span>
    * <span data-ttu-id="29dbe-142">Služby</span><span class="sxs-lookup"><span data-stu-id="29dbe-142">Services</span></span>
      * <span data-ttu-id="29dbe-143">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="29dbe-143">Controllers</span></span>
        * <span data-ttu-id="29dbe-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="29dbe-144">HomeController.cs</span></span>
      * <span data-ttu-id="29dbe-145">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="29dbe-145">Views</span></span>
        * <span data-ttu-id="29dbe-146">Domů</span><span class="sxs-lookup"><span data-stu-id="29dbe-146">Home</span></span>
          * <span data-ttu-id="29dbe-147">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-147">Index.cshtml</span></span>

<span data-ttu-id="29dbe-148">I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="29dbe-149">Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:</span><span class="sxs-lookup"><span data-stu-id="29dbe-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="29dbe-150">Přidružte kontroler k oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-150">Associate the controller with an Area</span></span>

<span data-ttu-id="29dbe-151">Řadiče oblasti se označují pomocí atributu [&lbrack;oblasti&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="29dbe-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="29dbe-152">Přidat trasu oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-152">Add Area route</span></span>

<span data-ttu-id="29dbe-153">Trasy oblastí obvykle používají [konvenční směrování](xref:mvc/controllers/routing#cr) namísto [Směrování atributů](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="29dbe-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="29dbe-154">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="29dbe-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="29dbe-155">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="29dbe-156">`{area:...}` lze použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:</span><span class="sxs-lookup"><span data-stu-id="29dbe-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="29dbe-157">V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="29dbe-158">Použití `{area:...}` s `MapControllerRoute`:</span><span class="sxs-lookup"><span data-stu-id="29dbe-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="29dbe-159">Je nejjednodušším mechanismem přidávání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="29dbe-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="29dbe-160">Vyhledá všechny řadiče pomocí atributu `[Area("Area name")]`.</span><span class="sxs-lookup"><span data-stu-id="29dbe-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="29dbe-161">Následující kód používá <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> k vytvoření dvou tras pojmenované oblasti:</span><span class="sxs-lookup"><span data-stu-id="29dbe-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="29dbe-162">Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="29dbe-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="29dbe-163">Generování propojení s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="29dbe-163">Link generation with MVC areas</span></span>

<span data-ttu-id="29dbe-164">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) zobrazuje generaci odkazů s určenou oblastí:</span><span class="sxs-lookup"><span data-stu-id="29dbe-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="29dbe-165">Ukázka ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) , které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="29dbe-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="29dbe-166">Předchozí odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-166">The preceding links.</span></span>
* <span data-ttu-id="29dbe-167">Odkazy podobné předchozímu, s výjimkou `area`, nejsou zadány.</span><span class="sxs-lookup"><span data-stu-id="29dbe-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="29dbe-168">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="29dbe-169">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.</span><span class="sxs-lookup"><span data-stu-id="29dbe-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="29dbe-170">Pokud oblast nebo kontroler neurčíte, směrování závisí na [okolních](xref:mvc/controllers/routing#ambient) hodnotách.</span><span class="sxs-lookup"><span data-stu-id="29dbe-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="29dbe-171">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="29dbe-172">V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávná propojení se značkou, která neurčuje oblast.</span><span class="sxs-lookup"><span data-stu-id="29dbe-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="29dbe-173">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="29dbe-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="29dbe-174">Sdílené rozložení pro oblasti používající _ViewStart soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="29dbe-175">Chcete-li sdílet společné rozložení pro celou aplikaci, ponechte *_ViewStart. cshtml* v [kořenové složce aplikace](#arf).</span><span class="sxs-lookup"><span data-stu-id="29dbe-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="29dbe-176">Další informace najdete v tématu <xref:mvc/views/layout></span><span class="sxs-lookup"><span data-stu-id="29dbe-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="29dbe-177">Kořenová složka aplikace</span><span class="sxs-lookup"><span data-stu-id="29dbe-177">Application root folder</span></span>

<span data-ttu-id="29dbe-178">Kořenová složka aplikace je složka obsahující *Startup.cs* ve webové aplikaci vytvořené pomocí šablon ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29dbe-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="29dbe-179">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="29dbe-180">*/Views/_ViewImports. cshtml*, pro MVC a */Pages/_ViewImports. cshtml* pro Razor Pages, se neimportují do zobrazení v oblastech.</span><span class="sxs-lookup"><span data-stu-id="29dbe-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="29dbe-181">Pomocí jednoho z následujících přístupů poskytněte importy zobrazení do všech zobrazení:</span><span class="sxs-lookup"><span data-stu-id="29dbe-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="29dbe-182">Přidejte *_ViewImports. cshtml* do [kořenové složky aplikace](#arf).</span><span class="sxs-lookup"><span data-stu-id="29dbe-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="29dbe-183">*_ViewImports. cshtml* v kořenové složce aplikace se bude vztahovat na všechna zobrazení v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29dbe-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="29dbe-184">Zkopírujte soubor *_ViewImports. cshtml* do příslušné složky zobrazení v oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="29dbe-185">Soubor *_ViewImports. cshtml* obvykle obsahuje [pomocníky s Tagy](xref:mvc/views/tag-helpers/intro) importy, `@using`a `@inject` příkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="29dbe-186">Další informace najdete v tématu [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="29dbe-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="29dbe-187">Změna výchozí složky oblasti, kde jsou uložená zobrazení</span><span class="sxs-lookup"><span data-stu-id="29dbe-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="29dbe-188">Následující kód změní výchozí složku oblasti z `"Areas"` na `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="29dbe-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="29dbe-189">Oblasti s Razor Pages</span><span class="sxs-lookup"><span data-stu-id="29dbe-189">Areas with Razor Pages</span></span>

<span data-ttu-id="29dbe-190">Oblasti s Razor Pages vyžadují `Areas/<area name>/Pages` složku v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="29dbe-191">Pro [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)se používá tato struktura složek:</span><span class="sxs-lookup"><span data-stu-id="29dbe-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="29dbe-192">Název projektu</span><span class="sxs-lookup"><span data-stu-id="29dbe-192">Project name</span></span>
  * <span data-ttu-id="29dbe-193">Oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-193">Areas</span></span>
    * <span data-ttu-id="29dbe-194">Produkty</span><span class="sxs-lookup"><span data-stu-id="29dbe-194">Products</span></span>
      * <span data-ttu-id="29dbe-195">Stránky</span><span class="sxs-lookup"><span data-stu-id="29dbe-195">Pages</span></span>
        * <span data-ttu-id="29dbe-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="29dbe-196">_ViewImports</span></span>
        * <span data-ttu-id="29dbe-197">Informace</span><span class="sxs-lookup"><span data-stu-id="29dbe-197">About</span></span>
        * <span data-ttu-id="29dbe-198">Index</span><span class="sxs-lookup"><span data-stu-id="29dbe-198">Index</span></span>
    * <span data-ttu-id="29dbe-199">Služby</span><span class="sxs-lookup"><span data-stu-id="29dbe-199">Services</span></span>
      * <span data-ttu-id="29dbe-200">Stránky</span><span class="sxs-lookup"><span data-stu-id="29dbe-200">Pages</span></span>
        * <span data-ttu-id="29dbe-201">Správa</span><span class="sxs-lookup"><span data-stu-id="29dbe-201">Manage</span></span>
          * <span data-ttu-id="29dbe-202">Informace</span><span class="sxs-lookup"><span data-stu-id="29dbe-202">About</span></span>
          * <span data-ttu-id="29dbe-203">Index</span><span class="sxs-lookup"><span data-stu-id="29dbe-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="29dbe-204">Vytváření odkazů pomocí Razor Pages a oblastí</span><span class="sxs-lookup"><span data-stu-id="29dbe-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="29dbe-205">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci propojení se zadanou oblastí (například `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="29dbe-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="29dbe-206">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="29dbe-207">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="29dbe-208">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="29dbe-209">Když není oblast zadaná, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="29dbe-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="29dbe-210">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="29dbe-211">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="29dbe-212">Zvažte například odkazy vygenerované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="29dbe-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="29dbe-213">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="29dbe-213">For the preceding code:</span></span>

* <span data-ttu-id="29dbe-214">Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že poslední požadavek byl pro stránku v oblasti `Services`.</span><span class="sxs-lookup"><span data-stu-id="29dbe-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="29dbe-215">Například `/Services/Manage/`, `/Services/Manage/Index`nebo `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="29dbe-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="29dbe-216">Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že poslední požadavek byl pro stránku v `/Home`.</span><span class="sxs-lookup"><span data-stu-id="29dbe-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="29dbe-217">Kód pochází z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="29dbe-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="29dbe-218">Importovat obory názvů a pomocníka značek s _ViewImportsm souborem</span><span class="sxs-lookup"><span data-stu-id="29dbe-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="29dbe-219">Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou stránku Razor ve složce.</span><span class="sxs-lookup"><span data-stu-id="29dbe-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="29dbe-220">Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="29dbe-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="29dbe-221">Následující kód ukazuje stránku */Services/Manage/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="29dbe-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="29dbe-222">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="29dbe-222">In the preceding markup:</span></span>

* <span data-ttu-id="29dbe-223">K určení modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) je nutné použít plně kvalifikovaný název domény.</span><span class="sxs-lookup"><span data-stu-id="29dbe-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="29dbe-224">[Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený pomocí `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="29dbe-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="29dbe-225">V ukázkovém stažení oblast produkty obsahuje následující *_ViewImports soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="29dbe-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="29dbe-226">Následující kód ukazuje stránku */Products/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="29dbe-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="29dbe-227">V předchozím souboru je direktiva Namespace a `@addTagHelper` naimportována do souboru pomocí souboru *areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="29dbe-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="29dbe-228">Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="29dbe-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="29dbe-229">Sdílené rozložení pro Razor Pages oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="29dbe-230">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="29dbe-231">Oblasti publikování</span><span class="sxs-lookup"><span data-stu-id="29dbe-231">Publishing Areas</span></span>

<span data-ttu-id="29dbe-232">Všechny soubory \*. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, pokud je `<Project Sdk="Microsoft.NET.Sdk.Web">` obsaženo v souboru \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="29dbe-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29dbe-233">Oblasti jsou funkcí ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="29dbe-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="29dbe-234">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy, `area`, `controller` a `action` nebo `page`stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="29dbe-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="29dbe-235">Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní sadu Razor Pages, řadičů, zobrazení a modelů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="29dbe-236">Oblast je ve skutečnosti strukturou uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="29dbe-237">Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="29dbe-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="29dbe-238">Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="29dbe-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="29dbe-239">Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně.</span><span class="sxs-lookup"><span data-stu-id="29dbe-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="29dbe-240">Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="29dbe-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="29dbe-241">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče, Razor Pages a modely.</span><span class="sxs-lookup"><span data-stu-id="29dbe-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="29dbe-242">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="29dbe-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="29dbe-243">Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="29dbe-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="29dbe-244">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="29dbe-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="29dbe-245">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="29dbe-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="29dbe-246">Ukázka stažení poskytuje základní aplikaci pro oblasti testování.</span><span class="sxs-lookup"><span data-stu-id="29dbe-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="29dbe-247">Pokud používáte Razor Pages, přečtěte si téma [oblasti s Razor Pages](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="29dbe-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="29dbe-248">Oblasti pro řadiče se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="29dbe-248">Areas for controllers with views</span></span>

<span data-ttu-id="29dbe-249">Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="29dbe-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="29dbe-250">[Struktura složky oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="29dbe-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="29dbe-251">Řadiče s atributem [`[Area]`](#attribute) k přidružení kontroleru k oblasti:</span><span class="sxs-lookup"><span data-stu-id="29dbe-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="29dbe-252">[Trasa oblasti přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="29dbe-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="29dbe-253">Struktura složky oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-253">Area folder structure</span></span>

<span data-ttu-id="29dbe-254">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="29dbe-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="29dbe-255">Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="29dbe-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="29dbe-256">Název projektu</span><span class="sxs-lookup"><span data-stu-id="29dbe-256">Project name</span></span>
  * <span data-ttu-id="29dbe-257">Oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-257">Areas</span></span>
    * <span data-ttu-id="29dbe-258">Produkty</span><span class="sxs-lookup"><span data-stu-id="29dbe-258">Products</span></span>
      * <span data-ttu-id="29dbe-259">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="29dbe-259">Controllers</span></span>
        * <span data-ttu-id="29dbe-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="29dbe-260">HomeController.cs</span></span>
        * <span data-ttu-id="29dbe-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="29dbe-261">ManageController.cs</span></span>
      * <span data-ttu-id="29dbe-262">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="29dbe-262">Views</span></span>
        * <span data-ttu-id="29dbe-263">Domů</span><span class="sxs-lookup"><span data-stu-id="29dbe-263">Home</span></span>
          * <span data-ttu-id="29dbe-264">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-264">Index.cshtml</span></span>
        * <span data-ttu-id="29dbe-265">Správa</span><span class="sxs-lookup"><span data-stu-id="29dbe-265">Manage</span></span>
          * <span data-ttu-id="29dbe-266">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-266">Index.cshtml</span></span>
          * <span data-ttu-id="29dbe-267">O. cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-267">About.cshtml</span></span>
    * <span data-ttu-id="29dbe-268">Služby</span><span class="sxs-lookup"><span data-stu-id="29dbe-268">Services</span></span>
      * <span data-ttu-id="29dbe-269">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="29dbe-269">Controllers</span></span>
        * <span data-ttu-id="29dbe-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="29dbe-270">HomeController.cs</span></span>
      * <span data-ttu-id="29dbe-271">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="29dbe-271">Views</span></span>
        * <span data-ttu-id="29dbe-272">Domů</span><span class="sxs-lookup"><span data-stu-id="29dbe-272">Home</span></span>
          * <span data-ttu-id="29dbe-273">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-273">Index.cshtml</span></span>

<span data-ttu-id="29dbe-274">I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="29dbe-275">Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:</span><span class="sxs-lookup"><span data-stu-id="29dbe-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="29dbe-276">Přidružte kontroler k oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-276">Associate the controller with an Area</span></span>

<span data-ttu-id="29dbe-277">Řadiče oblasti se označují pomocí atributu [&lbrack;oblasti&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="29dbe-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="29dbe-278">Přidat trasu oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-278">Add Area route</span></span>

<span data-ttu-id="29dbe-279">Trasy oblastí obvykle používají konvenční směrování namísto směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="29dbe-280">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="29dbe-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="29dbe-281">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="29dbe-282">`{area:...}` lze použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:</span><span class="sxs-lookup"><span data-stu-id="29dbe-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="29dbe-283">V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="29dbe-284">Použití `{area:...}` je nejjednodušším mechanismem přidávání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="29dbe-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="29dbe-285">Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> k vytvoření dvou tras pojmenované oblasti:</span><span class="sxs-lookup"><span data-stu-id="29dbe-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="29dbe-286">Pokud používáte `MapAreaRoute` s ASP.NET Core 2,2, přečtěte si [Tento problém GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="29dbe-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="29dbe-287">Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="29dbe-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="29dbe-288">Generování propojení s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="29dbe-288">Link generation with MVC areas</span></span>

<span data-ttu-id="29dbe-289">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje generaci odkazů s určenou oblastí:</span><span class="sxs-lookup"><span data-stu-id="29dbe-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="29dbe-290">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29dbe-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="29dbe-291">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="29dbe-292">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="29dbe-293">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.</span><span class="sxs-lookup"><span data-stu-id="29dbe-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="29dbe-294">Pokud oblast nebo kontroler neurčíte, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="29dbe-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="29dbe-295">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="29dbe-296">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="29dbe-297">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="29dbe-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="29dbe-298">Sdílené rozložení pro oblasti používající _ViewStart soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="29dbe-299">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="29dbe-300">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="29dbe-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="29dbe-301">Ve svém standardním umístění */Views/_ViewImports. cshtml* se nevztahuje na oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="29dbe-302">Chcete-li použít běžné [pomocníky značek](xref:mvc/views/tag-helpers/intro), `@using`nebo `@inject` ve vaší oblasti, zajistěte, aby se v [zobrazeních oblastí](xref:mvc/views/layout#importing-shared-directives)používal správný soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="29dbe-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="29dbe-303">Pokud chcete stejné chování ve všech zobrazeních, přesuňte */Views/_ViewImports. cshtml* do kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="29dbe-304">Změna výchozí složky oblasti, kde jsou uložená zobrazení</span><span class="sxs-lookup"><span data-stu-id="29dbe-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="29dbe-305">Následující kód změní výchozí složku oblasti z `"Areas"` na `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="29dbe-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="29dbe-306">Oblasti s Razor Pages</span><span class="sxs-lookup"><span data-stu-id="29dbe-306">Areas with Razor Pages</span></span>

<span data-ttu-id="29dbe-307">Oblasti s Razor Pages vyžadují `Areas/<area name>/Pages` složku v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="29dbe-308">Pro [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)se používá tato struktura složek:</span><span class="sxs-lookup"><span data-stu-id="29dbe-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="29dbe-309">Název projektu</span><span class="sxs-lookup"><span data-stu-id="29dbe-309">Project name</span></span>
  * <span data-ttu-id="29dbe-310">Oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-310">Areas</span></span>
    * <span data-ttu-id="29dbe-311">Produkty</span><span class="sxs-lookup"><span data-stu-id="29dbe-311">Products</span></span>
      * <span data-ttu-id="29dbe-312">Stránky</span><span class="sxs-lookup"><span data-stu-id="29dbe-312">Pages</span></span>
        * <span data-ttu-id="29dbe-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="29dbe-313">_ViewImports</span></span>
        * <span data-ttu-id="29dbe-314">Informace</span><span class="sxs-lookup"><span data-stu-id="29dbe-314">About</span></span>
        * <span data-ttu-id="29dbe-315">Index</span><span class="sxs-lookup"><span data-stu-id="29dbe-315">Index</span></span>
    * <span data-ttu-id="29dbe-316">Služby</span><span class="sxs-lookup"><span data-stu-id="29dbe-316">Services</span></span>
      * <span data-ttu-id="29dbe-317">Stránky</span><span class="sxs-lookup"><span data-stu-id="29dbe-317">Pages</span></span>
        * <span data-ttu-id="29dbe-318">Správa</span><span class="sxs-lookup"><span data-stu-id="29dbe-318">Manage</span></span>
          * <span data-ttu-id="29dbe-319">Informace</span><span class="sxs-lookup"><span data-stu-id="29dbe-319">About</span></span>
          * <span data-ttu-id="29dbe-320">Index</span><span class="sxs-lookup"><span data-stu-id="29dbe-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="29dbe-321">Vytváření odkazů pomocí Razor Pages a oblastí</span><span class="sxs-lookup"><span data-stu-id="29dbe-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="29dbe-322">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci propojení se zadanou oblastí (například `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="29dbe-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="29dbe-323">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="29dbe-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="29dbe-324">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="29dbe-325">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="29dbe-326">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="29dbe-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="29dbe-327">Když není oblast zadaná, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="29dbe-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="29dbe-328">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="29dbe-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="29dbe-329">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="29dbe-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="29dbe-330">Zvažte například odkazy vygenerované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="29dbe-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="29dbe-331">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="29dbe-331">For the preceding code:</span></span>

* <span data-ttu-id="29dbe-332">Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že poslední požadavek byl pro stránku v oblasti `Services`.</span><span class="sxs-lookup"><span data-stu-id="29dbe-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="29dbe-333">Například `/Services/Manage/`, `/Services/Manage/Index`nebo `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="29dbe-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="29dbe-334">Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že poslední požadavek byl pro stránku v `/Home`.</span><span class="sxs-lookup"><span data-stu-id="29dbe-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="29dbe-335">Kód pochází z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="29dbe-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="29dbe-336">Importovat obory názvů a pomocníka značek s _ViewImportsm souborem</span><span class="sxs-lookup"><span data-stu-id="29dbe-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="29dbe-337">Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou stránku Razor ve složce.</span><span class="sxs-lookup"><span data-stu-id="29dbe-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="29dbe-338">Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="29dbe-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="29dbe-339">Následující kód ukazuje stránku */Services/Manage/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="29dbe-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="29dbe-340">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="29dbe-340">In the preceding markup:</span></span>

* <span data-ttu-id="29dbe-341">K určení modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) je nutné použít plně kvalifikovaný název domény.</span><span class="sxs-lookup"><span data-stu-id="29dbe-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="29dbe-342">[Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený pomocí `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="29dbe-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="29dbe-343">V ukázkovém stažení oblast produkty obsahuje následující *_ViewImports soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="29dbe-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="29dbe-344">Následující kód ukazuje stránku */Products/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="29dbe-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="29dbe-345">V předchozím souboru je direktiva Namespace a `@addTagHelper` naimportována do souboru pomocí souboru *areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="29dbe-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="29dbe-346">Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="29dbe-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="29dbe-347">Sdílené rozložení pro Razor Pages oblasti</span><span class="sxs-lookup"><span data-stu-id="29dbe-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="29dbe-348">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="29dbe-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="29dbe-349">Oblasti publikování</span><span class="sxs-lookup"><span data-stu-id="29dbe-349">Publishing Areas</span></span>

<span data-ttu-id="29dbe-350">Všechny soubory \*. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, pokud je `<Project Sdk="Microsoft.NET.Sdk.Web">` obsaženo v souboru \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="29dbe-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
