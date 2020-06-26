---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se jedná o funkci ASP.NET MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/areas
ms.openlocfilehash: f4471f54e92b940ecb0eabab82f03e0c77a0509f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406404"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="889b7-103">Oblasti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="889b7-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="889b7-104">Od [Dhananjay Kumarem](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="889b7-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="889b7-105">Oblasti jsou funkce ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného:</span><span class="sxs-lookup"><span data-stu-id="889b7-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="889b7-106">Obor názvů pro směrování</span><span class="sxs-lookup"><span data-stu-id="889b7-106">Namespace for routing.</span></span>
* <span data-ttu-id="889b7-107">Struktura složek pro zobrazení a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="889b7-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="889b7-108">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy, `area` , do `controller` a `action` nebo do Razor stránky `page` .</span><span class="sxs-lookup"><span data-stu-id="889b7-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="889b7-109">Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní sadu Razor stránek, řadičů, zobrazení a modelů.</span><span class="sxs-lookup"><span data-stu-id="889b7-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="889b7-110">Oblast je ve skutečnosti strukturou uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="889b7-111">Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="889b7-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="889b7-112">Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="889b7-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="889b7-113">Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně.</span><span class="sxs-lookup"><span data-stu-id="889b7-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="889b7-114">Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="889b7-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="889b7-115">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče, Razor stránky a modely.</span><span class="sxs-lookup"><span data-stu-id="889b7-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="889b7-116">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="889b7-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="889b7-117">Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="889b7-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="889b7-118">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="889b7-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="889b7-119">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="889b7-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="889b7-120">Ukázka stažení poskytuje základní aplikaci pro oblasti testování.</span><span class="sxs-lookup"><span data-stu-id="889b7-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="889b7-121">Pokud používáte Razor stránky, přečtěte si téma [oblasti se Razor stránkami](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="889b7-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="889b7-122">Oblasti pro řadiče se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="889b7-122">Areas for controllers with views</span></span>

<span data-ttu-id="889b7-123">Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="889b7-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="889b7-124">[Struktura složky oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="889b7-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="889b7-125">Řadiče s [`[Area]`](#attribute) atributem k přidružení kontroleru k oblasti:</span><span class="sxs-lookup"><span data-stu-id="889b7-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="889b7-126">[Trasa oblasti přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="889b7-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="889b7-127">Struktura složky oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-127">Area folder structure</span></span>

<span data-ttu-id="889b7-128">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="889b7-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="889b7-129">Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="889b7-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="889b7-130">Název projektu</span><span class="sxs-lookup"><span data-stu-id="889b7-130">Project name</span></span>
  * <span data-ttu-id="889b7-131">Oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-131">Areas</span></span>
    * <span data-ttu-id="889b7-132">Produkty</span><span class="sxs-lookup"><span data-stu-id="889b7-132">Products</span></span>
      * <span data-ttu-id="889b7-133">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="889b7-133">Controllers</span></span>
        * <span data-ttu-id="889b7-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="889b7-134">HomeController.cs</span></span>
        * <span data-ttu-id="889b7-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="889b7-135">ManageController.cs</span></span>
      * <span data-ttu-id="889b7-136">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="889b7-136">Views</span></span>
        * <span data-ttu-id="889b7-137">Domů</span><span class="sxs-lookup"><span data-stu-id="889b7-137">Home</span></span>
          * <span data-ttu-id="889b7-138">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-138">Index.cshtml</span></span>
        * <span data-ttu-id="889b7-139">Spravovat</span><span class="sxs-lookup"><span data-stu-id="889b7-139">Manage</span></span>
          * <span data-ttu-id="889b7-140">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-140">Index.cshtml</span></span>
          * <span data-ttu-id="889b7-141">O. cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-141">About.cshtml</span></span>
    * <span data-ttu-id="889b7-142">Služby</span><span class="sxs-lookup"><span data-stu-id="889b7-142">Services</span></span>
      * <span data-ttu-id="889b7-143">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="889b7-143">Controllers</span></span>
        * <span data-ttu-id="889b7-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="889b7-144">HomeController.cs</span></span>
      * <span data-ttu-id="889b7-145">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="889b7-145">Views</span></span>
        * <span data-ttu-id="889b7-146">Domů</span><span class="sxs-lookup"><span data-stu-id="889b7-146">Home</span></span>
          * <span data-ttu-id="889b7-147">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-147">Index.cshtml</span></span>

<span data-ttu-id="889b7-148">I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="889b7-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="889b7-149">Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:</span><span class="sxs-lookup"><span data-stu-id="889b7-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="889b7-150">Přidružte kontroler k oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-150">Associate the controller with an Area</span></span>

<span data-ttu-id="889b7-151">Řadiče oblastí jsou označeny atributem [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="889b7-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="889b7-152">Přidat trasu oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-152">Add Area route</span></span>

<span data-ttu-id="889b7-153">Trasy oblastí obvykle používají [konvenční směrování](xref:mvc/controllers/routing#cr) namísto [Směrování atributů](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="889b7-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="889b7-154">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="889b7-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="889b7-155">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="889b7-156">`{area:...}`dá se použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:</span><span class="sxs-lookup"><span data-stu-id="889b7-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="889b7-157">V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="889b7-158">Použití `{area:...}` s `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="889b7-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="889b7-159">Je nejjednodušším mechanismem přidávání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="889b7-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="889b7-160">Vyhledá všechny řadiče s `[Area("Area name")]` atributem.</span><span class="sxs-lookup"><span data-stu-id="889b7-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="889b7-161">Následující kód používá <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> k vytvoření dvou tras pojmenované oblasti:</span><span class="sxs-lookup"><span data-stu-id="889b7-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="889b7-162">Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="889b7-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="889b7-163">Generování propojení s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="889b7-163">Link generation with MVC areas</span></span>

<span data-ttu-id="889b7-164">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) zobrazuje generaci odkazů s určenou oblastí:</span><span class="sxs-lookup"><span data-stu-id="889b7-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="889b7-165">Ukázka ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) , které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="889b7-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="889b7-166">Předchozí odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-166">The preceding links.</span></span>
* <span data-ttu-id="889b7-167">Odkazy podobné předchozímu, s výjimkou `area` nejsou zadány.</span><span class="sxs-lookup"><span data-stu-id="889b7-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="889b7-168">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="889b7-169">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.</span><span class="sxs-lookup"><span data-stu-id="889b7-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="889b7-170">Pokud oblast nebo kontroler neurčíte, směrování závisí na [okolních](xref:mvc/controllers/routing#ambient) hodnotách.</span><span class="sxs-lookup"><span data-stu-id="889b7-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="889b7-171">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="889b7-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="889b7-172">V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávná propojení se značkou, která neurčuje oblast.</span><span class="sxs-lookup"><span data-stu-id="889b7-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="889b7-173">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="889b7-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="889b7-174">Sdílené rozložení pro oblasti používající _ViewStart soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="889b7-175">Chcete-li sdílet společné rozložení pro celou aplikaci, ponechte *_ViewStart. cshtml* v [kořenové složce aplikace](#arf).</span><span class="sxs-lookup"><span data-stu-id="889b7-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="889b7-176">Další informace najdete v tématu <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="889b7-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="889b7-177">Kořenová složka aplikace</span><span class="sxs-lookup"><span data-stu-id="889b7-177">Application root folder</span></span>

<span data-ttu-id="889b7-178">Kořenová složka aplikace je složka obsahující *Startup.cs* ve webové aplikaci vytvořené pomocí šablon ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="889b7-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="889b7-179">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="889b7-180">*/Views/_ViewImports. cshtml*, pro MVC a */Pages/_ViewImports. cshtml* pro Razor stránky, se neimportují do zobrazení v oblastech.</span><span class="sxs-lookup"><span data-stu-id="889b7-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="889b7-181">Pomocí jednoho z následujících přístupů poskytněte importy zobrazení do všech zobrazení:</span><span class="sxs-lookup"><span data-stu-id="889b7-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="889b7-182">Přidejte *_ViewImports. cshtml* do [kořenové složky aplikace](#arf).</span><span class="sxs-lookup"><span data-stu-id="889b7-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="889b7-183">*_ViewImports. cshtml* v kořenové složce aplikace se bude vztahovat na všechna zobrazení v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="889b7-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="889b7-184">Zkopírujte soubor *_ViewImports. cshtml* do příslušné složky zobrazení v oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="889b7-185">Soubor *_ViewImports. cshtml* obvykle obsahuje [pomocníky](xref:mvc/views/tag-helpers/intro) s příkazy Imports, `@using` a `@inject` .</span><span class="sxs-lookup"><span data-stu-id="889b7-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="889b7-186">Další informace najdete v tématu [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="889b7-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="889b7-187">Změna výchozí složky oblasti, kde jsou uložená zobrazení</span><span class="sxs-lookup"><span data-stu-id="889b7-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="889b7-188">Následující kód změní výchozí složku oblasti z `"Areas"` na `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="889b7-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="889b7-189">Oblasti se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="889b7-189">Areas with Razor Pages</span></span>

<span data-ttu-id="889b7-190">Oblasti se Razor stránkami vyžadují `Areas/<area name>/Pages` složku v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="889b7-191">Pro [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)se používá tato struktura složek:</span><span class="sxs-lookup"><span data-stu-id="889b7-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="889b7-192">Název projektu</span><span class="sxs-lookup"><span data-stu-id="889b7-192">Project name</span></span>
  * <span data-ttu-id="889b7-193">Oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-193">Areas</span></span>
    * <span data-ttu-id="889b7-194">Produkty</span><span class="sxs-lookup"><span data-stu-id="889b7-194">Products</span></span>
      * <span data-ttu-id="889b7-195">Stránky</span><span class="sxs-lookup"><span data-stu-id="889b7-195">Pages</span></span>
        * <span data-ttu-id="889b7-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="889b7-196">_ViewImports</span></span>
        * <span data-ttu-id="889b7-197">Informace</span><span class="sxs-lookup"><span data-stu-id="889b7-197">About</span></span>
        * <span data-ttu-id="889b7-198">Index</span><span class="sxs-lookup"><span data-stu-id="889b7-198">Index</span></span>
    * <span data-ttu-id="889b7-199">Služby</span><span class="sxs-lookup"><span data-stu-id="889b7-199">Services</span></span>
      * <span data-ttu-id="889b7-200">Stránky</span><span class="sxs-lookup"><span data-stu-id="889b7-200">Pages</span></span>
        * <span data-ttu-id="889b7-201">Spravovat</span><span class="sxs-lookup"><span data-stu-id="889b7-201">Manage</span></span>
          * <span data-ttu-id="889b7-202">Informace</span><span class="sxs-lookup"><span data-stu-id="889b7-202">About</span></span>
          * <span data-ttu-id="889b7-203">Index</span><span class="sxs-lookup"><span data-stu-id="889b7-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="889b7-204">Generování odkazů pomocí Razor stránek a oblastí</span><span class="sxs-lookup"><span data-stu-id="889b7-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="889b7-205">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci odkazů se zadanou oblastí (například `asp-area="Products"` ):</span><span class="sxs-lookup"><span data-stu-id="889b7-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="889b7-206">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="889b7-207">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="889b7-208">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="889b7-209">Když není oblast zadaná, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="889b7-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="889b7-210">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="889b7-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="889b7-211">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="889b7-212">Zvažte například odkazy vygenerované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="889b7-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="889b7-213">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="889b7-213">For the preceding code:</span></span>

* <span data-ttu-id="889b7-214">Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že byl poslední požadavek na stránku v `Services` oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="889b7-215">Například,, `/Services/Manage/` `/Services/Manage/Index` nebo `/Services/Manage/About` .</span><span class="sxs-lookup"><span data-stu-id="889b7-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="889b7-216">Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že byl poslední požadavek na stránku v `/Home` .</span><span class="sxs-lookup"><span data-stu-id="889b7-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="889b7-217">Kód pochází z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="889b7-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="889b7-218">Importovat obory názvů a pomocníka značek s _ViewImportsm souborem</span><span class="sxs-lookup"><span data-stu-id="889b7-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="889b7-219">Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou Razor stránku ve složce.</span><span class="sxs-lookup"><span data-stu-id="889b7-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="889b7-220">Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="889b7-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="889b7-221">Následující kód ukazuje stránku */Services/Manage/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="889b7-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="889b7-222">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="889b7-222">In the preceding markup:</span></span>

* <span data-ttu-id="889b7-223">K určení modelu () je nutné použít plně kvalifikovaný název domény `@model RPareas.Areas.Services.Pages.Manage.AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="889b7-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="889b7-224">[Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="889b7-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="889b7-225">V ukázkovém stažení oblast produkty obsahuje následující *_ViewImports soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="889b7-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="889b7-226">Následující kód ukazuje stránku */Products/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="889b7-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="889b7-227">V předchozím souboru je obor názvů a `@addTagHelper` direktiva importován do souboru pomocí souboru *areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="889b7-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="889b7-228">Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="889b7-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="889b7-229">Sdílené rozložení pro Razor oblasti stránek</span><span class="sxs-lookup"><span data-stu-id="889b7-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="889b7-230">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="889b7-231">Oblasti publikování</span><span class="sxs-lookup"><span data-stu-id="889b7-231">Publishing Areas</span></span>

<span data-ttu-id="889b7-232">Všechny soubory \*. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, pokud `<Project Sdk="Microsoft.NET.Sdk.Web">` je součástí souboru \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="889b7-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="889b7-233">Oblasti jsou funkcí ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="889b7-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="889b7-234">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy, `area` , do `controller` a `action` nebo do Razor stránky `page` .</span><span class="sxs-lookup"><span data-stu-id="889b7-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="889b7-235">Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní sadu Razor stránek, řadičů, zobrazení a modelů.</span><span class="sxs-lookup"><span data-stu-id="889b7-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="889b7-236">Oblast je ve skutečnosti strukturou uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="889b7-237">Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="889b7-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="889b7-238">Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="889b7-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="889b7-239">Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně.</span><span class="sxs-lookup"><span data-stu-id="889b7-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="889b7-240">Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="889b7-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="889b7-241">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče, Razor stránky a modely.</span><span class="sxs-lookup"><span data-stu-id="889b7-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="889b7-242">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="889b7-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="889b7-243">Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="889b7-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="889b7-244">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="889b7-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="889b7-245">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="889b7-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="889b7-246">Ukázka stažení poskytuje základní aplikaci pro oblasti testování.</span><span class="sxs-lookup"><span data-stu-id="889b7-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="889b7-247">Pokud používáte Razor stránky, přečtěte si téma [oblasti se Razor stránkami](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="889b7-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="889b7-248">Oblasti pro řadiče se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="889b7-248">Areas for controllers with views</span></span>

<span data-ttu-id="889b7-249">Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="889b7-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="889b7-250">[Struktura složky oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="889b7-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="889b7-251">Řadiče s [`[Area]`](#attribute) atributem k přidružení kontroleru k oblasti:</span><span class="sxs-lookup"><span data-stu-id="889b7-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="889b7-252">[Trasa oblasti přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="889b7-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="889b7-253">Struktura složky oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-253">Area folder structure</span></span>

<span data-ttu-id="889b7-254">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="889b7-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="889b7-255">Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="889b7-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="889b7-256">Název projektu</span><span class="sxs-lookup"><span data-stu-id="889b7-256">Project name</span></span>
  * <span data-ttu-id="889b7-257">Oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-257">Areas</span></span>
    * <span data-ttu-id="889b7-258">Produkty</span><span class="sxs-lookup"><span data-stu-id="889b7-258">Products</span></span>
      * <span data-ttu-id="889b7-259">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="889b7-259">Controllers</span></span>
        * <span data-ttu-id="889b7-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="889b7-260">HomeController.cs</span></span>
        * <span data-ttu-id="889b7-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="889b7-261">ManageController.cs</span></span>
      * <span data-ttu-id="889b7-262">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="889b7-262">Views</span></span>
        * <span data-ttu-id="889b7-263">Domů</span><span class="sxs-lookup"><span data-stu-id="889b7-263">Home</span></span>
          * <span data-ttu-id="889b7-264">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-264">Index.cshtml</span></span>
        * <span data-ttu-id="889b7-265">Spravovat</span><span class="sxs-lookup"><span data-stu-id="889b7-265">Manage</span></span>
          * <span data-ttu-id="889b7-266">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-266">Index.cshtml</span></span>
          * <span data-ttu-id="889b7-267">O. cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-267">About.cshtml</span></span>
    * <span data-ttu-id="889b7-268">Služby</span><span class="sxs-lookup"><span data-stu-id="889b7-268">Services</span></span>
      * <span data-ttu-id="889b7-269">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="889b7-269">Controllers</span></span>
        * <span data-ttu-id="889b7-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="889b7-270">HomeController.cs</span></span>
      * <span data-ttu-id="889b7-271">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="889b7-271">Views</span></span>
        * <span data-ttu-id="889b7-272">Domů</span><span class="sxs-lookup"><span data-stu-id="889b7-272">Home</span></span>
          * <span data-ttu-id="889b7-273">Soubor Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-273">Index.cshtml</span></span>

<span data-ttu-id="889b7-274">I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="889b7-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="889b7-275">Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:</span><span class="sxs-lookup"><span data-stu-id="889b7-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="889b7-276">Přidružte kontroler k oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-276">Associate the controller with an Area</span></span>

<span data-ttu-id="889b7-277">Řadiče oblastí jsou označeny atributem [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="889b7-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="889b7-278">Přidat trasu oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-278">Add Area route</span></span>

<span data-ttu-id="889b7-279">Trasy oblastí obvykle používají konvenční směrování namísto směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="889b7-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="889b7-280">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="889b7-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="889b7-281">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="889b7-282">`{area:...}`dá se použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:</span><span class="sxs-lookup"><span data-stu-id="889b7-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="889b7-283">V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="889b7-284">Použití je nejjednodušším `{area:...}` mechanismem přidávání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="889b7-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="889b7-285">Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> k vytvoření dvou tras pojmenované oblasti:</span><span class="sxs-lookup"><span data-stu-id="889b7-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="889b7-286">Při použití `MapAreaRoute` s ASP.NET Core 2,2 se podívejte na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="889b7-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="889b7-287">Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="889b7-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="889b7-288">Generování propojení s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="889b7-288">Link generation with MVC areas</span></span>

<span data-ttu-id="889b7-289">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje generaci odkazů s určenou oblastí:</span><span class="sxs-lookup"><span data-stu-id="889b7-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="889b7-290">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="889b7-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="889b7-291">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="889b7-292">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="889b7-293">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.</span><span class="sxs-lookup"><span data-stu-id="889b7-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="889b7-294">Pokud oblast nebo kontroler neurčíte, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="889b7-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="889b7-295">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="889b7-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="889b7-296">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="889b7-297">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="889b7-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="889b7-298">Sdílené rozložení pro oblasti používající _ViewStart soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="889b7-299">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="889b7-300">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="889b7-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="889b7-301">Ve svém standardním umístění */Views/_ViewImports. cshtml* se nevztahuje na oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="889b7-302">Chcete-li použít běžné [pomocníky značek](xref:mvc/views/tag-helpers/intro), `@using` nebo `@inject` ve vaší oblasti, zajistěte, aby se v [zobrazeních oblastí](xref:mvc/views/layout#importing-shared-directives)používal vhodný *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="889b7-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="889b7-303">Pokud chcete stejné chování ve všech zobrazeních, přesuňte */Views/_ViewImports. cshtml* do kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="889b7-304">Změna výchozí složky oblasti, kde jsou uložená zobrazení</span><span class="sxs-lookup"><span data-stu-id="889b7-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="889b7-305">Následující kód změní výchozí složku oblasti z `"Areas"` na `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="889b7-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="889b7-306">Oblasti se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="889b7-306">Areas with Razor Pages</span></span>

<span data-ttu-id="889b7-307">Oblasti se Razor stránkami vyžadují `Areas/<area name>/Pages` složku v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="889b7-308">Pro [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)se používá tato struktura složek:</span><span class="sxs-lookup"><span data-stu-id="889b7-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="889b7-309">Název projektu</span><span class="sxs-lookup"><span data-stu-id="889b7-309">Project name</span></span>
  * <span data-ttu-id="889b7-310">Oblasti</span><span class="sxs-lookup"><span data-stu-id="889b7-310">Areas</span></span>
    * <span data-ttu-id="889b7-311">Produkty</span><span class="sxs-lookup"><span data-stu-id="889b7-311">Products</span></span>
      * <span data-ttu-id="889b7-312">Stránky</span><span class="sxs-lookup"><span data-stu-id="889b7-312">Pages</span></span>
        * <span data-ttu-id="889b7-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="889b7-313">_ViewImports</span></span>
        * <span data-ttu-id="889b7-314">Informace</span><span class="sxs-lookup"><span data-stu-id="889b7-314">About</span></span>
        * <span data-ttu-id="889b7-315">Index</span><span class="sxs-lookup"><span data-stu-id="889b7-315">Index</span></span>
    * <span data-ttu-id="889b7-316">Služby</span><span class="sxs-lookup"><span data-stu-id="889b7-316">Services</span></span>
      * <span data-ttu-id="889b7-317">Stránky</span><span class="sxs-lookup"><span data-stu-id="889b7-317">Pages</span></span>
        * <span data-ttu-id="889b7-318">Spravovat</span><span class="sxs-lookup"><span data-stu-id="889b7-318">Manage</span></span>
          * <span data-ttu-id="889b7-319">Informace</span><span class="sxs-lookup"><span data-stu-id="889b7-319">About</span></span>
          * <span data-ttu-id="889b7-320">Index</span><span class="sxs-lookup"><span data-stu-id="889b7-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="889b7-321">Generování odkazů pomocí Razor stránek a oblastí</span><span class="sxs-lookup"><span data-stu-id="889b7-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="889b7-322">Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci odkazů se zadanou oblastí (například `asp-area="Products"` ):</span><span class="sxs-lookup"><span data-stu-id="889b7-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="889b7-323">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="889b7-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="889b7-324">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="889b7-325">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="889b7-326">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="889b7-327">Když není oblast zadaná, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="889b7-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="889b7-328">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="889b7-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="889b7-329">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="889b7-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="889b7-330">Zvažte například odkazy vygenerované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="889b7-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="889b7-331">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="889b7-331">For the preceding code:</span></span>

* <span data-ttu-id="889b7-332">Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že byl poslední požadavek na stránku v `Services` oblasti.</span><span class="sxs-lookup"><span data-stu-id="889b7-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="889b7-333">Například,, `/Services/Manage/` `/Services/Manage/Index` nebo `/Services/Manage/About` .</span><span class="sxs-lookup"><span data-stu-id="889b7-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="889b7-334">Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že byl poslední požadavek na stránku v `/Home` .</span><span class="sxs-lookup"><span data-stu-id="889b7-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="889b7-335">Kód pochází z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="889b7-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="889b7-336">Importovat obory názvů a pomocníka značek s _ViewImportsm souborem</span><span class="sxs-lookup"><span data-stu-id="889b7-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="889b7-337">Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou Razor stránku ve složce.</span><span class="sxs-lookup"><span data-stu-id="889b7-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="889b7-338">Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="889b7-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="889b7-339">Následující kód ukazuje stránku */Services/Manage/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="889b7-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="889b7-340">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="889b7-340">In the preceding markup:</span></span>

* <span data-ttu-id="889b7-341">K určení modelu () je nutné použít plně kvalifikovaný název domény `@model RPareas.Areas.Services.Pages.Manage.AboutModel` .</span><span class="sxs-lookup"><span data-stu-id="889b7-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="889b7-342">[Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="889b7-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="889b7-343">V ukázkovém stažení oblast produkty obsahuje následující *_ViewImports soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="889b7-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="889b7-344">Následující kód ukazuje stránku */Products/about* Razor :</span><span class="sxs-lookup"><span data-stu-id="889b7-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="889b7-345">V předchozím souboru je obor názvů a `@addTagHelper` direktiva importován do souboru pomocí souboru *areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="889b7-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="889b7-346">Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="889b7-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="889b7-347">Sdílené rozložení pro Razor oblasti stránek</span><span class="sxs-lookup"><span data-stu-id="889b7-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="889b7-348">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="889b7-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="889b7-349">Oblasti publikování</span><span class="sxs-lookup"><span data-stu-id="889b7-349">Publishing Areas</span></span>

<span data-ttu-id="889b7-350">Všechny soubory \*. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, pokud `<Project Sdk="Microsoft.NET.Sdk.Web">` je součástí souboru \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="889b7-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
