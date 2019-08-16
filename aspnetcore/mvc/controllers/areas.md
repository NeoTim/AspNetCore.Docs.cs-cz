---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se jedná o funkci ASP.NET MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).
ms.author: riande
ms.date: 08/16/2019
uid: mvc/controllers/areas
ms.openlocfilehash: d0af3092776ee09469c879fffd3047c50b1a59b4
ms.sourcegitcommit: 4cb0c7e74355f2e87c60e2a196f842b937247a99
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69545804"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="d1a24-103">Oblasti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1a24-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="d1a24-104">Od [Dhananjay Kumarem](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d1a24-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d1a24-105">Oblasti jsou funkcí ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="d1a24-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="d1a24-106">Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího `area`parametru trasy,, do `controller` a `action` nebo na stránku `page`Razor.</span><span class="sxs-lookup"><span data-stu-id="d1a24-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="d1a24-107">Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní sadu Razor Pages, řadičů, zobrazení a modelů.</span><span class="sxs-lookup"><span data-stu-id="d1a24-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="d1a24-108">Oblast je ve skutečnosti strukturou uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="d1a24-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="d1a24-109">Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách.</span><span class="sxs-lookup"><span data-stu-id="d1a24-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="d1a24-110">Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="d1a24-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="d1a24-111">Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně.</span><span class="sxs-lookup"><span data-stu-id="d1a24-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="d1a24-112">Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="d1a24-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="d1a24-113">Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče, Razor Pages a modely.</span><span class="sxs-lookup"><span data-stu-id="d1a24-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="d1a24-114">Zvažte použití oblastí v projektu, když:</span><span class="sxs-lookup"><span data-stu-id="d1a24-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="d1a24-115">Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="d1a24-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="d1a24-116">Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.</span><span class="sxs-lookup"><span data-stu-id="d1a24-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="d1a24-117">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d1a24-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="d1a24-118">Ukázka stažení poskytuje základní aplikaci pro oblasti testování.</span><span class="sxs-lookup"><span data-stu-id="d1a24-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="d1a24-119">Pokud používáte Razor Pages, přečtěte si téma [oblasti s Razor Pages](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="d1a24-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="d1a24-120">Oblasti pro řadiče se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="d1a24-120">Areas for controllers with views</span></span>

<span data-ttu-id="d1a24-121">Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:</span><span class="sxs-lookup"><span data-stu-id="d1a24-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="d1a24-122">[Struktura složky oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="d1a24-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="d1a24-123">Řadiče upravené pomocí [ &lbrack;atributu Area&rbrack; ](#attribute) k přidružení kontroleru k oblasti:</span><span class="sxs-lookup"><span data-stu-id="d1a24-123">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="d1a24-124">[Trasa oblasti přidaná ke spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="d1a24-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="d1a24-125">Struktura složky oblasti</span><span class="sxs-lookup"><span data-stu-id="d1a24-125">Area folder structure</span></span>

<span data-ttu-id="d1a24-126">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="d1a24-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="d1a24-127">Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d1a24-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="d1a24-128">Název projektu</span><span class="sxs-lookup"><span data-stu-id="d1a24-128">Project name</span></span>
  * <span data-ttu-id="d1a24-129">Oblasti</span><span class="sxs-lookup"><span data-stu-id="d1a24-129">Areas</span></span>
    * <span data-ttu-id="d1a24-130">Produktech</span><span class="sxs-lookup"><span data-stu-id="d1a24-130">Products</span></span>
      * <span data-ttu-id="d1a24-131">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="d1a24-131">Controllers</span></span>
        * <span data-ttu-id="d1a24-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d1a24-132">HomeController.cs</span></span>
        * <span data-ttu-id="d1a24-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="d1a24-133">ManageController.cs</span></span>
      * <span data-ttu-id="d1a24-134">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="d1a24-134">Views</span></span>
        * <span data-ttu-id="d1a24-135">Domů</span><span class="sxs-lookup"><span data-stu-id="d1a24-135">Home</span></span>
          * <span data-ttu-id="d1a24-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d1a24-136">Index.cshtml</span></span>
        * <span data-ttu-id="d1a24-137">Správy</span><span class="sxs-lookup"><span data-stu-id="d1a24-137">Manage</span></span>
          * <span data-ttu-id="d1a24-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d1a24-138">Index.cshtml</span></span>
          * <span data-ttu-id="d1a24-139">O. cshtml</span><span class="sxs-lookup"><span data-stu-id="d1a24-139">About.cshtml</span></span>
    * <span data-ttu-id="d1a24-140">Služby</span><span class="sxs-lookup"><span data-stu-id="d1a24-140">Services</span></span>
      * <span data-ttu-id="d1a24-141">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="d1a24-141">Controllers</span></span>
        * <span data-ttu-id="d1a24-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d1a24-142">HomeController.cs</span></span>
      * <span data-ttu-id="d1a24-143">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="d1a24-143">Views</span></span>
        * <span data-ttu-id="d1a24-144">Domů</span><span class="sxs-lookup"><span data-stu-id="d1a24-144">Home</span></span>
          * <span data-ttu-id="d1a24-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d1a24-145">Index.cshtml</span></span>

<span data-ttu-id="d1a24-146">I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="d1a24-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="d1a24-147">Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:</span><span class="sxs-lookup"><span data-stu-id="d1a24-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="d1a24-148">Umístění nezobrazených složek, jako jsou *řadiče* a *modely* , **nezáleží**.</span><span class="sxs-lookup"><span data-stu-id="d1a24-148">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="d1a24-149">Například složka *řadiče* a *modely* není vyžadována.</span><span class="sxs-lookup"><span data-stu-id="d1a24-149">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="d1a24-150">Obsah *řadičů* a *modelů* je kód, který je zkompilován do knihovny DLL.</span><span class="sxs-lookup"><span data-stu-id="d1a24-150">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="d1a24-151">Obsah *zobrazení* není zkompilován, dokud není proveden požadavek na toto zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d1a24-151">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="d1a24-152">Přidružte kontroler k oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-152">Associate the controller with an Area</span></span>

<span data-ttu-id="d1a24-153">Řadiče oblastí jsou označeny [ &lbrack;atributem Area&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="d1a24-153">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="d1a24-154">Přidat trasu oblasti</span><span class="sxs-lookup"><span data-stu-id="d1a24-154">Add Area route</span></span>

<span data-ttu-id="d1a24-155">Trasy oblastí obvykle používají konvenční směrování namísto směrování atributů.</span><span class="sxs-lookup"><span data-stu-id="d1a24-155">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="d1a24-156">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="d1a24-156">Conventional routing is order-dependent.</span></span> <span data-ttu-id="d1a24-157">Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-157">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="d1a24-158">`{area:...}`dá se použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:</span><span class="sxs-lookup"><span data-stu-id="d1a24-158">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="d1a24-159">V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-159">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="d1a24-160">Použití `{area:...}` je nejjednodušším mechanismem přidávání směrování do oblastí.</span><span class="sxs-lookup"><span data-stu-id="d1a24-160">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="d1a24-161">Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> k vytvoření dvou tras pojmenované oblasti:</span><span class="sxs-lookup"><span data-stu-id="d1a24-161">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="d1a24-162">Při použití `MapAreaRoute` s ASP.NET Core 2,2 se podívejte na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="d1a24-162">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="d1a24-163">Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="d1a24-163">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="d1a24-164">Generování propojení s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="d1a24-164">Link generation with MVC areas</span></span>

<span data-ttu-id="d1a24-165">Následující kód v ukázkovém souboru [ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje generaci odkazů s určenou oblastí:</span><span class="sxs-lookup"><span data-stu-id="d1a24-165">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d1a24-166">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1a24-166">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="d1a24-167">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-167">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d1a24-168">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="d1a24-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d1a24-169">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.</span><span class="sxs-lookup"><span data-stu-id="d1a24-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="d1a24-170">Pokud oblast nebo kontroler neurčíte, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="d1a24-170">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d1a24-171">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="d1a24-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d1a24-172">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="d1a24-172">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="d1a24-173">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="d1a24-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="d1a24-174">Sdílené rozložení pro oblasti s použitím souboru _ViewStart. cshtml</span><span class="sxs-lookup"><span data-stu-id="d1a24-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="d1a24-175">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="d1a24-175">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="d1a24-176">_ViewImports. cshtml</span><span class="sxs-lookup"><span data-stu-id="d1a24-176">_ViewImports.cshtml</span></span>

<span data-ttu-id="d1a24-177">Ve svém standardním umístění se */views/_ViewImports.cshtml* nevztahuje na oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-177">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="d1a24-178">Chcete-li [](xref:mvc/views/tag-helpers/intro)použít běžné pomocníky `@using`značek, `@inject` nebo ve vaší oblasti, zajistěte, aby se v [zobrazeních oblastí](xref:mvc/views/layout#importing-shared-directives)používal správný soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d1a24-178">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="d1a24-179">Pokud chcete stejné chování ve všech zobrazeních, přesuňte */views/_ViewImports.cshtml* do kořenového adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="d1a24-179">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="d1a24-180">Změna výchozí složky oblasti, kde jsou uložená zobrazení</span><span class="sxs-lookup"><span data-stu-id="d1a24-180">Change default area folder where views are stored</span></span>

<span data-ttu-id="d1a24-181">Následující kód změní výchozí složku oblasti z `"Areas"` na: `"MyAreas"`</span><span class="sxs-lookup"><span data-stu-id="d1a24-181">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="d1a24-182">Oblasti s Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d1a24-182">Areas with Razor Pages</span></span>

<span data-ttu-id="d1a24-183">Oblasti s Razor Pages v kořenu aplikace vyžadují *<area name>oblast nebo složku/Pages* .</span><span class="sxs-lookup"><span data-stu-id="d1a24-183">Areas with Razor Pages require an *Areas/<area name>/Pages* folder in the root of the app.</span></span> <span data-ttu-id="d1a24-184">Pro [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)se používá tato struktura složek:</span><span class="sxs-lookup"><span data-stu-id="d1a24-184">The following folder structure is used with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="d1a24-185">Název projektu</span><span class="sxs-lookup"><span data-stu-id="d1a24-185">Project name</span></span>
  * <span data-ttu-id="d1a24-186">Oblasti</span><span class="sxs-lookup"><span data-stu-id="d1a24-186">Areas</span></span>
    * <span data-ttu-id="d1a24-187">Produktech</span><span class="sxs-lookup"><span data-stu-id="d1a24-187">Products</span></span>
      * <span data-ttu-id="d1a24-188">Stránky</span><span class="sxs-lookup"><span data-stu-id="d1a24-188">Pages</span></span>
        * <span data-ttu-id="d1a24-189">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="d1a24-189">_ViewImports</span></span>
        * <span data-ttu-id="d1a24-190">O produktu</span><span class="sxs-lookup"><span data-stu-id="d1a24-190">About</span></span>
        * <span data-ttu-id="d1a24-191">Index</span><span class="sxs-lookup"><span data-stu-id="d1a24-191">Index</span></span>
    * <span data-ttu-id="d1a24-192">Služby</span><span class="sxs-lookup"><span data-stu-id="d1a24-192">Services</span></span>
      * <span data-ttu-id="d1a24-193">Stránky</span><span class="sxs-lookup"><span data-stu-id="d1a24-193">Pages</span></span>
        * <span data-ttu-id="d1a24-194">Správy</span><span class="sxs-lookup"><span data-stu-id="d1a24-194">Manage</span></span>
          * <span data-ttu-id="d1a24-195">O produktu</span><span class="sxs-lookup"><span data-stu-id="d1a24-195">About</span></span>
          * <span data-ttu-id="d1a24-196">Index</span><span class="sxs-lookup"><span data-stu-id="d1a24-196">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="d1a24-197">Vytváření odkazů pomocí Razor Pages a oblastí</span><span class="sxs-lookup"><span data-stu-id="d1a24-197">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="d1a24-198">Následující kód v ukázkovém souboru [ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci odkazů se zadanou oblastí (například `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="d1a24-198">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d1a24-199">Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1a24-199">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="d1a24-200">Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-200">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d1a24-201">Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy.</span><span class="sxs-lookup"><span data-stu-id="d1a24-201">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d1a24-202">Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-202">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="d1a24-203">Když není oblast zadaná, směrování závisí na *okolních* hodnotách.</span><span class="sxs-lookup"><span data-stu-id="d1a24-203">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d1a24-204">Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="d1a24-204">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d1a24-205">V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="d1a24-205">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="d1a24-206">Zvažte například odkazy vygenerované z následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="d1a24-206">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="d1a24-207">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="d1a24-207">For the preceding code:</span></span>

* <span data-ttu-id="d1a24-208">Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že byl poslední požadavek na stránku `Services` v oblasti.</span><span class="sxs-lookup"><span data-stu-id="d1a24-208">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="d1a24-209">Například `/Services/Manage/` `/Services/Manage/About`, ,nebo.`/Services/Manage/Index`</span><span class="sxs-lookup"><span data-stu-id="d1a24-209">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="d1a24-210">Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že byl poslední požadavek na stránku `/Home`v.</span><span class="sxs-lookup"><span data-stu-id="d1a24-210">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="d1a24-211">Kód pochází z ukázkového [stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="d1a24-211">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="d1a24-212">Importovat obory názvů a pomocníka značek pomocí souboru _ViewImports</span><span class="sxs-lookup"><span data-stu-id="d1a24-212">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="d1a24-213">Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou stránku Razor ve složce.</span><span class="sxs-lookup"><span data-stu-id="d1a24-213">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="d1a24-214">Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d1a24-214">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="d1a24-215">Následující kód ukazuje stránku */Services/Manage/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="d1a24-215">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="d1a24-216">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="d1a24-216">In the preceding markup:</span></span>

* <span data-ttu-id="d1a24-217">K určení modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) je nutné použít plně kvalifikovaný název domény.</span><span class="sxs-lookup"><span data-stu-id="d1a24-217">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="d1a24-218">[Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="d1a24-218">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="d1a24-219">V ukázkovém stažení oblast produkty obsahuje následující soubor *_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="d1a24-219">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="d1a24-220">Následující kód ukazuje stránku */Products/about* Razor:</span><span class="sxs-lookup"><span data-stu-id="d1a24-220">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="d1a24-221">V předchozím souboru je obor názvů a `@addTagHelper` direktiva importován do souboru pomocí souboru areas */Products/Pages/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d1a24-221">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="d1a24-222">Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d1a24-222">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="d1a24-223">Sdílené rozložení pro Razor Pages oblasti</span><span class="sxs-lookup"><span data-stu-id="d1a24-223">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="d1a24-224">Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.</span><span class="sxs-lookup"><span data-stu-id="d1a24-224">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="d1a24-225">Oblasti publikování</span><span class="sxs-lookup"><span data-stu-id="d1a24-225">Publishing Areas</span></span>

<span data-ttu-id="d1a24-226">Všechny soubory \*. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, `<Project Sdk="Microsoft.NET.Sdk.Web">` Pokud je součástí souboru \*. csproj.</span><span class="sxs-lookup"><span data-stu-id="d1a24-226">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
