---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak oblasti jsou používány pro organizaci související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení) funkce služby technologie ASP.NET MVC.
ms.author: riande
ms.date: 05/06/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 35c7682861f7392b0bcda7326e4d7f5ccc356bda
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212586"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="8ab5e-103">Oblasti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8ab5e-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="8ab5e-104">Podle [Dhananjay Kumar](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ab5e-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8ab5e-105">Oblasti jsou funkce služby ASP.NET použita k uspořádání související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="8ab5e-106">Pomocí oblastí vytvoří hierarchii pro účely směrování tak, že přidáte další parametr trasy, `area`do `controller` a `action` nebo stránky Razor `page`.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="8ab5e-107">Oblasti poskytují způsob, jak dělit základní webové aplikace v ASP.NET do menších skupin funkční, každý s vlastní sadou stránek Razor, řadičů, zobrazení a modely.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="8ab5e-108">Oblast je v podstatě strukturu uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="8ab5e-109">V projektu webové aplikace ASP.NET Core jsou v různých složkách uchovávají logické komponenty, jako jsou stránky, modelu, Kontroleru a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="8ab5e-110">ASP.NET Core runtime používá konvence pojmenování a vytvořit tak relaci mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="8ab5e-111">Pro velké aplikace může být výhodné rozdělit na samostatné vysoké úrovně funkční oblasti aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="8ab5e-112">Například e-commerce aplikace s několika obchodními jednotkami, jako je například checkout, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="8ab5e-113">Každá z těchto jednotek mají své vlastní oblasti tak, aby obsahovala zobrazení, kontrolery, Razor Pages a modely.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="8ab5e-114">Zvažte použití oblasti v projektu při:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="8ab5e-115">Aplikace je tvořeno několika vysoké úrovně funkčnosti součásti, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="8ab5e-116">Chcete rozdělit aplikace tak, aby každá funkční oblast je možné pracovat nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="8ab5e-117">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="8ab5e-118">Stažení ukázkové poskytuje základní aplikace pro testování oblasti.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="8ab5e-119">Pokud používáte pro stránky Razor, přečtěte si téma [oblasti se stránkami Razor](#areas-with-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="8ab5e-120">Oblasti pro kontrolery se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="8ab5e-120">Areas for controllers with views</span></span>

<span data-ttu-id="8ab5e-121">Typické webové aplikace ASP.NET Core pomocí oblastí, kontrolerů a zobrazení obsahuje následující prvky:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="8ab5e-122">[Strukturu složek oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="8ab5e-123">Kontrolery upravené pomocí [ &lbrack;oblasti&rbrack; ](#attribute) atribut asociovat řadič se serverem oblasti:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-123">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="8ab5e-124">[Oblasti trasa přidaná do spuštění](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="8ab5e-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="8ab5e-125">Struktura složek oblasti</span><span class="sxs-lookup"><span data-stu-id="8ab5e-125">Area folder structure</span></span>

<span data-ttu-id="8ab5e-126">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="8ab5e-127">Používat, bude podobný následujícímu strukturu složek:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="8ab5e-128">Název projektu</span><span class="sxs-lookup"><span data-stu-id="8ab5e-128">Project name</span></span>
  * <span data-ttu-id="8ab5e-129">Oblasti</span><span class="sxs-lookup"><span data-stu-id="8ab5e-129">Areas</span></span>
    * <span data-ttu-id="8ab5e-130">Produkty</span><span class="sxs-lookup"><span data-stu-id="8ab5e-130">Products</span></span>
      * <span data-ttu-id="8ab5e-131">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="8ab5e-131">Controllers</span></span>
        * <span data-ttu-id="8ab5e-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8ab5e-132">HomeController.cs</span></span>
        * <span data-ttu-id="8ab5e-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="8ab5e-133">ManageController.cs</span></span>
      * <span data-ttu-id="8ab5e-134">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="8ab5e-134">Views</span></span>
        * <span data-ttu-id="8ab5e-135">Domů</span><span class="sxs-lookup"><span data-stu-id="8ab5e-135">Home</span></span>
          * <span data-ttu-id="8ab5e-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8ab5e-136">Index.cshtml</span></span>
        * <span data-ttu-id="8ab5e-137">Správa</span><span class="sxs-lookup"><span data-stu-id="8ab5e-137">Manage</span></span>
          * <span data-ttu-id="8ab5e-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8ab5e-138">Index.cshtml</span></span>
          * <span data-ttu-id="8ab5e-139">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="8ab5e-139">About.cshtml</span></span>
    * <span data-ttu-id="8ab5e-140">Služby</span><span class="sxs-lookup"><span data-stu-id="8ab5e-140">Services</span></span>
      * <span data-ttu-id="8ab5e-141">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="8ab5e-141">Controllers</span></span>
        * <span data-ttu-id="8ab5e-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8ab5e-142">HomeController.cs</span></span>
      * <span data-ttu-id="8ab5e-143">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="8ab5e-143">Views</span></span>
        * <span data-ttu-id="8ab5e-144">Domů</span><span class="sxs-lookup"><span data-stu-id="8ab5e-144">Home</span></span>
          * <span data-ttu-id="8ab5e-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8ab5e-145">Index.cshtml</span></span>

<span data-ttu-id="8ab5e-146">Při předchozím rozložení je obvyklá, když pomocí oblastí, zobrazit soubory je potřeba použít tuto strukturu složek.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="8ab5e-147">Zobrazení zjišťování vyhledá odpovídající oblast zobrazení soubor v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="8ab5e-148">Umístění složky jiných zobrazení, jako jsou *řadiče* a *modely* nemá **není** záleží.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-148">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="8ab5e-149">Například *řadiče* a *modely* složky nejsou povinné.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-149">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="8ab5e-150">Obsah *řadiče* a *modely* je kód, který získá kompilovány do knihovny DLL.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-150">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="8ab5e-151">Obsah *zobrazení* není kompilována, dokud byla podána žádost do tohoto zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-151">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="8ab5e-152">Asociovat řadič se serverem oblast</span><span class="sxs-lookup"><span data-stu-id="8ab5e-152">Associate the controller with an Area</span></span>

<span data-ttu-id="8ab5e-153">Oblast řadiče jsou označeny [ &lbrack;oblasti&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) atribut:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-153">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="8ab5e-154">Přidat oblast směrování</span><span class="sxs-lookup"><span data-stu-id="8ab5e-154">Add Area route</span></span>

<span data-ttu-id="8ab5e-155">Cesty oblasti obvykle používají konvenční směrování místo směrováním atributů.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-155">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="8ab5e-156">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-156">Conventional routing is order-dependent.</span></span> <span data-ttu-id="8ab5e-157">Obecně platí trasy s oblastmi by měl umístit výše ve směrovací tabulce, jako jsou podrobnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-157">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="8ab5e-158">`{area:...}` slouží jako token v šablonách tras Pokud ve všech oblastech je jednotné místo adresy url:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-158">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="8ab5e-159">V předchozím kódu `exists` platí omezení, že postupu musí odpovídat oblast.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-159">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="8ab5e-160">Pomocí `{area:...}` je nejjednodušším mechanismus pro přidání směrování do oblasti.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-160">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="8ab5e-161">Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> vytvořit dva pojmenované oblasti trasy:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-161">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="8ab5e-162">Při použití `MapAreaRoute` 2.2 technologie ASP.NET Core, najdete v článku [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-162">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="8ab5e-163">Další informace najdete v tématu [oblasti směrování](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-163">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="8ab5e-164">Generování odkazů s oblastmi MVC</span><span class="sxs-lookup"><span data-stu-id="8ab5e-164">Link generation with MVC areas</span></span>

<span data-ttu-id="8ab5e-165">Následující kód z [vzorku ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje odkaz generace k zadané oblasti:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-165">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="8ab5e-166">Odkazy vygeneroval s předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-166">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="8ab5e-167">Zahrnuje vzorku ke stažení [částečné zobrazení](xref:mvc/views/partial) bez zadání oblasti, která obsahuje výše uvedené odkazy a stejné odkazy.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-167">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="8ab5e-168">Částečné zobrazení odkazuje [soubor rozložení](xref:mvc/views/layout), takže každé stránky v aplikaci zobrazí vygenerovaný odkazy.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="8ab5e-169">Odkazy generované bez zadání oblasti platí pouze při odkazování z stránku ve stejné oblasti a kontroler.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="8ab5e-170">Pokud oblast nebo kontroler není zadána, směrování závisí *okolí* hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-170">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="8ab5e-171">Aktuální hodnoty trasy z aktuální požadavek jsou považovány za okolí hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="8ab5e-172">V mnoha případech pro ukázkovou aplikaci pomocí okolí hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-172">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="8ab5e-173">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="8ab5e-174">Sdílené rozložení pro použití souboru soubor _ViewStart.cshtml oblasti</span><span class="sxs-lookup"><span data-stu-id="8ab5e-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="8ab5e-175">Chcete-li sdílet společný rozložení pro celé aplikace, přesuňte *soubor _ViewStart.cshtml* ke kořenové složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-175">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="8ab5e-176">Změna výchozí oblast složky pro uložení zobrazení</span><span class="sxs-lookup"><span data-stu-id="8ab5e-176">Change default area folder where views are stored</span></span>

<span data-ttu-id="8ab5e-177">Následující kód změní výchozí oblast složku z `"Areas"` k `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-177">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="8ab5e-178">Oblasti se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="8ab5e-178">Areas with Razor Pages</span></span>

<span data-ttu-id="8ab5e-179">Vyžadovat oblasti se stránkami Razor a *oblasti /&lt;název oblasti&gt;/stránky* složku v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-179">Areas with Razor Pages require and *Areas/&lt;area name&gt;/Pages* folder in the root of the app.</span></span> <span data-ttu-id="8ab5e-180">Následující strukturu složek se používá s [vzorku ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)</span><span class="sxs-lookup"><span data-stu-id="8ab5e-180">The following folder structure is used with the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)</span></span>

* <span data-ttu-id="8ab5e-181">Název projektu</span><span class="sxs-lookup"><span data-stu-id="8ab5e-181">Project name</span></span>
  * <span data-ttu-id="8ab5e-182">Oblasti</span><span class="sxs-lookup"><span data-stu-id="8ab5e-182">Areas</span></span>
    * <span data-ttu-id="8ab5e-183">Produkty</span><span class="sxs-lookup"><span data-stu-id="8ab5e-183">Products</span></span>
      * <span data-ttu-id="8ab5e-184">Stránky</span><span class="sxs-lookup"><span data-stu-id="8ab5e-184">Pages</span></span>
        * <span data-ttu-id="8ab5e-185">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="8ab5e-185">_ViewImports</span></span>
        * <span data-ttu-id="8ab5e-186">O produktu</span><span class="sxs-lookup"><span data-stu-id="8ab5e-186">About</span></span>
        * <span data-ttu-id="8ab5e-187">Index</span><span class="sxs-lookup"><span data-stu-id="8ab5e-187">Index</span></span>
    * <span data-ttu-id="8ab5e-188">Služby</span><span class="sxs-lookup"><span data-stu-id="8ab5e-188">Services</span></span>
      * <span data-ttu-id="8ab5e-189">Stránky</span><span class="sxs-lookup"><span data-stu-id="8ab5e-189">Pages</span></span>
        * <span data-ttu-id="8ab5e-190">Správa</span><span class="sxs-lookup"><span data-stu-id="8ab5e-190">Manage</span></span>
          * <span data-ttu-id="8ab5e-191">O produktu</span><span class="sxs-lookup"><span data-stu-id="8ab5e-191">About</span></span>
          * <span data-ttu-id="8ab5e-192">Index</span><span class="sxs-lookup"><span data-stu-id="8ab5e-192">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="8ab5e-193">Generování odkazů se stránkami Razor a oblasti</span><span class="sxs-lookup"><span data-stu-id="8ab5e-193">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="8ab5e-194">Následující kód z [vzorku ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje odkaz generace k zadané oblasti (například `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="8ab5e-194">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="8ab5e-195">Odkazy vygeneroval s předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-195">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="8ab5e-196">Zahrnuje vzorku ke stažení [částečné zobrazení](xref:mvc/views/partial) bez zadání oblasti, která obsahuje výše uvedené odkazy a stejné odkazy.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-196">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="8ab5e-197">Částečné zobrazení odkazuje [soubor rozložení](xref:mvc/views/layout), takže každé stránky v aplikaci zobrazí vygenerovaný odkazy.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-197">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="8ab5e-198">Odkazy generované bez zadání oblasti platí pouze při odkazování z stránku ve stejné oblasti.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-198">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="8ab5e-199">Pokud není zadána oblasti, směrování závisí *okolí* hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-199">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="8ab5e-200">Aktuální hodnoty trasy z aktuální požadavek jsou považovány za okolí hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-200">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="8ab5e-201">V mnoha případech pro ukázkovou aplikaci pomocí okolí hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-201">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="8ab5e-202">Představte si třeba odkazy generované z následující kód:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-202">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="8ab5e-203">Pro předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-203">For the preceding code:</span></span>

* <span data-ttu-id="8ab5e-204">Link nevygeneruje `<a asp-page="/Manage/About">` je správný, pouze když posledního požadavku byla pro stránku v `Services` oblasti.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-204">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="8ab5e-205">Například `/Services/Manage/`, `/Services/Manage/Index`, nebo `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-205">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="8ab5e-206">Link nevygeneruje `<a asp-page="/About">` je správný, pouze když posledního požadavku byla pro stránku v `/Home`.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-206">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="8ab5e-207">Kód je z [vzorku ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-207">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-viewimports-file"></a><span data-ttu-id="8ab5e-208">Import oboru názvů a pomocných rutin značek se souborem _ViewImports</span><span class="sxs-lookup"><span data-stu-id="8ab5e-208">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="8ab5e-209">A *_ViewImports* soubor lze přidat do každé oblasti *stránky* složce pro import oboru názvů a pomocných rutin značek na každou stránku Razor ve složce.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-209">A *_ViewImports* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="8ab5e-210">Vezměte v úvahu *služby* oblasti ukázek kódu, který neobsahuje *_ViewImports* souboru.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-210">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports* file.</span></span> <span data-ttu-id="8ab5e-211">Následující kód ukazuje */Services/Manage/About* stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-211">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="8ab5e-212">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-212">In the preceding markup:</span></span>

* <span data-ttu-id="8ab5e-213">K určení modelu musí použít plně kvalifikovaný název domény (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-213">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="8ab5e-214">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) ve jsou povolené `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="8ab5e-214">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="8ab5e-215">Oblasti produktů ve vzorku ke stažení obsahuje následující *_ViewImports* souboru:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-215">In the sample download, the Products area contains the following *_ViewImports* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="8ab5e-216">Následující kód ukazuje */produkty/o* stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="8ab5e-216">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="8ab5e-217">V předchozím soubor, obor názvů a `@addTagHelper` směrnice, je naimportován do souboru funkcí *Areas/Products/Pages/_ViewImports.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-217">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="8ab5e-218">Další informace najdete v tématu [Správa pomocné rutiny značky oboru](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [direktivy import sdílených](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="8ab5e-218">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="8ab5e-219">Sdílené rozložení pro oblasti stránek Razor</span><span class="sxs-lookup"><span data-stu-id="8ab5e-219">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="8ab5e-220">Chcete-li sdílet společný rozložení pro celé aplikace, přesuňte *soubor _ViewStart.cshtml* ke kořenové složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-220">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="8ab5e-221">Publikování oblasti</span><span class="sxs-lookup"><span data-stu-id="8ab5e-221">Publishing Areas</span></span>

<span data-ttu-id="8ab5e-222">Všechny `*.cshtml` a `wwwroot/**` souborů k publikování do výstupu, kdy `<Project Sdk="Microsoft.NET.Sdk.Web">` je součástí the.csproj\* souboru.</span><span class="sxs-lookup"><span data-stu-id="8ab5e-222">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the.csproj\* file.</span></span>
