---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se jedná o funkci ASP.NET MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 1066f4ce104e507abe63302fd3523a3a7a8dfde9
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828240"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="77a4b-103">Oblasti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="77a4b-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="77a4b-104">Od [Dhananjay Kumarem](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="77a4b-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="77a4b-105">Oblasti jsou funkcí ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="77a4b-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="77a4b-106">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího parametru trasy, `area`, `controller` a `action` nebo `page`stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="77a4b-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="77a4b-107">Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní sadu Razor Pages, řadičů, zobrazení a modelů.</span><span class="sxs-lookup"><span data-stu-id="77a4b-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="77a4b-108">Oblast je ve skutečnosti strukturou uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="77a4b-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="77a4b-109">Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="77a4b-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="77a4b-110">Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="77a4b-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="77a4b-111">Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně.</span><span class="sxs-lookup"><span data-stu-id="77a4b-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="77a4b-112">Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="77a4b-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="77a4b-113">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče, Razor Pages a modely.</span><span class="sxs-lookup"><span data-stu-id="77a4b-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="77a4b-114">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="77a4b-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="77a4b-115">Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="77a4b-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="77a4b-116">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="77a4b-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="77a4b-117">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="77a4b-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="77a4b-118">Ukázka stažení poskytuje základní aplikaci pro oblasti testování.</span><span class="sxs-lookup"><span data-stu-id="77a4b-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="77a4b-119">Pokud používáte Razor Pages, přečtěte si téma [oblasti s Razor Pages](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="77a4b-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="77a4b-120">Oblasti pro řadiče se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="77a4b-120">Areas for controllers with views</span></span>

<span data-ttu-id="77a4b-121">Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="77a4b-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="77a4b-122">[Struktura složky oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="77a4b-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="77a4b-123">Řadiče s atributem [`[Area]`](#attribute) k přidružení kontroleru k oblasti:</span><span class="sxs-lookup"><span data-stu-id="77a4b-123">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="77a4b-124">[Trasa oblasti přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="77a4b-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="77a4b-125">Struktura složky oblasti</span><span class="sxs-lookup"><span data-stu-id="77a4b-125">Area folder structure</span></span>

<span data-ttu-id="77a4b-126">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="77a4b-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="77a4b-127">Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="77a4b-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="77a4b-128">Název projektu</span><span class="sxs-lookup"><span data-stu-id="77a4b-128">Project name</span></span>
  * <span data-ttu-id="77a4b-129">Oblasti</span><span class="sxs-lookup"><span data-stu-id="77a4b-129">Areas</span></span>
    * <span data-ttu-id="77a4b-130">Produkty</span><span class="sxs-lookup"><span data-stu-id="77a4b-130">Products</span></span>
      * <span data-ttu-id="77a4b-131">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="77a4b-131">Controllers</span></span>
        * <span data-ttu-id="77a4b-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="77a4b-132">HomeController.cs</span></span>
        * <span data-ttu-id="77a4b-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="77a4b-133">ManageController.cs</span></span>
      * <span data-ttu-id="77a4b-134">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="77a4b-134">Views</span></span>
        * <span data-ttu-id="77a4b-135">Domovská stránka produktu</span><span class="sxs-lookup"><span data-stu-id="77a4b-135">Home</span></span>
          * <span data-ttu-id="77a4b-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="77a4b-136">Index.cshtml</span></span>
        * <span data-ttu-id="77a4b-137">Spravovat</span><span class="sxs-lookup"><span data-stu-id="77a4b-137">Manage</span></span>
          * <span data-ttu-id="77a4b-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="77a4b-138">Index.cshtml</span></span>
          * <span data-ttu-id="77a4b-139">O. cshtml</span><span class="sxs-lookup"><span data-stu-id="77a4b-139">About.cshtml</span></span>
    * <span data-ttu-id="77a4b-140">Služby</span><span class="sxs-lookup"><span data-stu-id="77a4b-140">Services</span></span>
      * <span data-ttu-id="77a4b-141">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="77a4b-141">Controllers</span></span>
        * <span data-ttu-id="77a4b-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="77a4b-142">HomeController.cs</span></span>
      * <span data-ttu-id="77a4b-143">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="77a4b-143">Views</span></span>
        * <span data-ttu-id="77a4b-144">Domovská stránka produktu</span><span class="sxs-lookup"><span data-stu-id="77a4b-144">Home</span></span>
          * <span data-ttu-id="77a4b-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="77a4b-145">Index.cshtml</span></span>

<span data-ttu-id="77a4b-146">I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="77a4b-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="77a4b-147">Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:</span><span class="sxs-lookup"><span data-stu-id="77a4b-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="77a4b-148">Přidružte kontroler k oblasti.</span><span class="sxs-lookup"><span data-stu-id="77a4b-148">Associate the controller with an Area</span></span>

<span data-ttu-id="77a4b-149">Řadiče oblasti se označují pomocí atributu [&lbrack;oblasti&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="77a4b-149">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="77a4b-150">Přidat trasu oblasti</span><span class="sxs-lookup"><span data-stu-id="77a4b-150">Add Area route</span></span>

<span data-ttu-id="77a4b-151">Trasy oblastí obvykle používají konvenční směrování namísto směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="77a4b-151">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="77a4b-152">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="77a4b-152">Conventional routing is order-dependent.</span></span> <span data-ttu-id="77a4b-153">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="77a4b-153">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="77a4b-154">`{area:...}` lze použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:</span><span class="sxs-lookup"><span data-stu-id="77a4b-154">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="77a4b-155">V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="77a4b-155">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="77a4b-156">Použití `{area:...}` je nejjednodušším mechanismem přidávání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="77a4b-156">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="77a4b-157">Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> k vytvoření dvou tras pojmenované oblasti:</span><span class="sxs-lookup"><span data-stu-id="77a4b-157">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="77a4b-158">Pokud používáte `MapAreaRoute` s ASP.NET Core 2,2, přečtěte si [Tento problém GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="77a4b-158">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="77a4b-159">Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="77a4b-159">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="77a4b-160">Generování propojení s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="77a4b-160">Link generation with MVC areas</span></span>

<span data-ttu-id="77a4b-161">Následující kód v [ukázkovém souboru ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje generaci odkazů s určenou oblastí:</span><span class="sxs-lookup"><span data-stu-id="77a4b-161">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="77a4b-162">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77a4b-162">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="77a4b-163">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="77a4b-163">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="77a4b-164">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="77a4b-164">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="77a4b-165">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.</span><span class="sxs-lookup"><span data-stu-id="77a4b-165">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="77a4b-166">Pokud oblast nebo kontroler neurčíte, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="77a4b-166">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="77a4b-167">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="77a4b-167">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="77a4b-168">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="77a4b-168">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="77a4b-169">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="77a4b-169">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="77a4b-170">Sdílené rozložení pro oblasti používající _ViewStart soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="77a4b-170">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="77a4b-171">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="77a4b-171">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="77a4b-172">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="77a4b-172">_ViewImports.cshtml</span></span>

<span data-ttu-id="77a4b-173">Ve svém standardním umístění */Views/_ViewImports. cshtml* se nevztahuje na oblasti.</span><span class="sxs-lookup"><span data-stu-id="77a4b-173">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="77a4b-174">Chcete-li použít běžné [pomocníky značek](xref:mvc/views/tag-helpers/intro), `@using`nebo `@inject` ve vaší oblasti, zajistěte, aby se v [zobrazeních oblastí](xref:mvc/views/layout#importing-shared-directives)používal správný soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="77a4b-174">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="77a4b-175">Pokud chcete stejné chování ve všech zobrazeních, přesuňte */Views/_ViewImports. cshtml* do kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="77a4b-175">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="77a4b-176">Změna výchozí složky oblasti, kde jsou uložená zobrazení</span><span class="sxs-lookup"><span data-stu-id="77a4b-176">Change default area folder where views are stored</span></span>

<span data-ttu-id="77a4b-177">Následující kód změní výchozí složku oblasti z `"Areas"` na `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="77a4b-177">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="77a4b-178">Oblasti s Razor Pages</span><span class="sxs-lookup"><span data-stu-id="77a4b-178">Areas with Razor Pages</span></span>

<span data-ttu-id="77a4b-179">Oblasti s Razor Pages v kořenu aplikace vyžadují *oblast nebo<area name>/Pages* složku.</span><span class="sxs-lookup"><span data-stu-id="77a4b-179">Areas with Razor Pages require an *Areas/<area name>/Pages* folder in the root of the app.</span></span> <span data-ttu-id="77a4b-180">Pro [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)se používá tato struktura složek:</span><span class="sxs-lookup"><span data-stu-id="77a4b-180">The following folder structure is used with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="77a4b-181">Název projektu</span><span class="sxs-lookup"><span data-stu-id="77a4b-181">Project name</span></span>
  * <span data-ttu-id="77a4b-182">Oblasti</span><span class="sxs-lookup"><span data-stu-id="77a4b-182">Areas</span></span>
    * <span data-ttu-id="77a4b-183">Produkty</span><span class="sxs-lookup"><span data-stu-id="77a4b-183">Products</span></span>
      * <span data-ttu-id="77a4b-184">Stránky</span><span class="sxs-lookup"><span data-stu-id="77a4b-184">Pages</span></span>
        * <span data-ttu-id="77a4b-185">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="77a4b-185">_ViewImports</span></span>
        * <span data-ttu-id="77a4b-186">Informace o nástroji</span><span class="sxs-lookup"><span data-stu-id="77a4b-186">About</span></span>
        * <span data-ttu-id="77a4b-187">Index</span><span class="sxs-lookup"><span data-stu-id="77a4b-187">Index</span></span>
    * <span data-ttu-id="77a4b-188">Služby</span><span class="sxs-lookup"><span data-stu-id="77a4b-188">Services</span></span>
      * <span data-ttu-id="77a4b-189">Stránky</span><span class="sxs-lookup"><span data-stu-id="77a4b-189">Pages</span></span>
        * <span data-ttu-id="77a4b-190">Spravovat</span><span class="sxs-lookup"><span data-stu-id="77a4b-190">Manage</span></span>
          * <span data-ttu-id="77a4b-191">Informace o nástroji</span><span class="sxs-lookup"><span data-stu-id="77a4b-191">About</span></span>
          * <span data-ttu-id="77a4b-192">Index</span><span class="sxs-lookup"><span data-stu-id="77a4b-192">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="77a4b-193">Vytváření odkazů pomocí Razor Pages a oblastí</span><span class="sxs-lookup"><span data-stu-id="77a4b-193">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="77a4b-194">Následující kód v [ukázkovém souboru ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci propojení se zadanou oblastí (například `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="77a4b-194">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="77a4b-195">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="77a4b-195">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="77a4b-196">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="77a4b-196">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="77a4b-197">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="77a4b-197">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="77a4b-198">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="77a4b-198">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="77a4b-199">Když není oblast zadaná, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="77a4b-199">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="77a4b-200">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="77a4b-200">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="77a4b-201">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="77a4b-201">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="77a4b-202">Zvažte například odkazy vygenerované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="77a4b-202">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="77a4b-203">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="77a4b-203">For the preceding code:</span></span>

* <span data-ttu-id="77a4b-204">Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že poslední požadavek byl pro stránku v oblasti `Services`.</span><span class="sxs-lookup"><span data-stu-id="77a4b-204">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="77a4b-205">Například `/Services/Manage/`, `/Services/Manage/Index`nebo `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="77a4b-205">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="77a4b-206">Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že poslední požadavek byl pro stránku v `/Home`.</span><span class="sxs-lookup"><span data-stu-id="77a4b-206">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="77a4b-207">Kód pochází z [ukázkového stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="77a4b-207">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="77a4b-208">Importovat obory názvů a pomocníka značek s _ViewImportsm souborem</span><span class="sxs-lookup"><span data-stu-id="77a4b-208">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="77a4b-209">Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou stránku Razor ve složce.</span><span class="sxs-lookup"><span data-stu-id="77a4b-209">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="77a4b-210">Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="77a4b-210">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="77a4b-211">Následující kód ukazuje stránku */Services/Manage/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="77a4b-211">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="77a4b-212">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="77a4b-212">In the preceding markup:</span></span>

* <span data-ttu-id="77a4b-213">K určení modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) je nutné použít plně kvalifikovaný název domény.</span><span class="sxs-lookup"><span data-stu-id="77a4b-213">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="77a4b-214">[Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený pomocí `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="77a4b-214">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="77a4b-215">V ukázkovém stažení oblast produkty obsahuje následující *_ViewImports soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="77a4b-215">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="77a4b-216">Následující kód ukazuje stránku */Products/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="77a4b-216">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="77a4b-217">V předchozím souboru je direktiva Namespace a `@addTagHelper` naimportována do souboru pomocí souboru *areas/Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="77a4b-217">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="77a4b-218">Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="77a4b-218">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="77a4b-219">Sdílené rozložení pro Razor Pages oblasti</span><span class="sxs-lookup"><span data-stu-id="77a4b-219">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="77a4b-220">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="77a4b-220">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="77a4b-221">Oblasti publikování</span><span class="sxs-lookup"><span data-stu-id="77a4b-221">Publishing Areas</span></span>

<span data-ttu-id="77a4b-222">Všechny soubory \*. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, pokud je `<Project Sdk="Microsoft.NET.Sdk.Web">` obsaženo v souboru \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="77a4b-222">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
