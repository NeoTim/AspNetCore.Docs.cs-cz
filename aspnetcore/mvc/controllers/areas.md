---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak oblasti jsou používány pro organizaci související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení) funkce služby technologie ASP.NET MVC.
ms.author: riande
ms.date: 02/14/2019
uid: mvc/controllers/areas
ms.openlocfilehash: c21eed04ea68512515da262b6b6895dc1a821039
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833524"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="8befc-103">Oblasti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8befc-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="8befc-104">Podle [Dhananjay Kumar](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8befc-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8befc-105">Oblasti jsou funkce služby ASP.NET použita k uspořádání související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení).</span><span class="sxs-lookup"><span data-stu-id="8befc-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="8befc-106">Pomocí oblastí vytvoří hierarchii pro účely směrování tak, že přidáte další parametr trasy, `area`do `controller` a `action` nebo stránky Razor `page`.</span><span class="sxs-lookup"><span data-stu-id="8befc-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="8befc-107">Oblasti poskytují způsob, jak dělit základní webové aplikace v ASP.NET do menších skupin funkční, každý s vlastní sadou stránek Razor, řadičů, zobrazení a modely.</span><span class="sxs-lookup"><span data-stu-id="8befc-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="8befc-108">Oblast je v podstatě strukturu uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="8befc-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="8befc-109">V projektu webové aplikace ASP.NET Core jsou v různých složkách uchovávají logické komponenty, jako jsou stránky, modelu, Kontroleru a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8befc-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="8befc-110">ASP.NET Core runtime používá konvence pojmenování a vytvořit tak relaci mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="8befc-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="8befc-111">Pro velké aplikace může být výhodné rozdělit na samostatné vysoké úrovně funkční oblasti aplikace.</span><span class="sxs-lookup"><span data-stu-id="8befc-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="8befc-112">Například e-commerce aplikace s několika obchodními jednotkami, jako je například checkout, fakturace a hledání.</span><span class="sxs-lookup"><span data-stu-id="8befc-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="8befc-113">Každá z těchto jednotek mají své vlastní oblasti tak, aby obsahovala zobrazení, kontrolery, Razor Pages a modely.</span><span class="sxs-lookup"><span data-stu-id="8befc-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="8befc-114">Zvažte použití oblasti v projektu při:</span><span class="sxs-lookup"><span data-stu-id="8befc-114">Consider using Areas in an project when:</span></span>

* <span data-ttu-id="8befc-115">Aplikace je tvořeno několika vysoké úrovně funkčnosti součásti, které je možné logicky oddělit.</span><span class="sxs-lookup"><span data-stu-id="8befc-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="8befc-116">Chcete rozdělit aplikace tak, aby každá funkční oblast je možné pracovat nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="8befc-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="8befc-117">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8befc-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="8befc-118">Stažení ukázkové poskytuje základní aplikace pro testování oblasti.</span><span class="sxs-lookup"><span data-stu-id="8befc-118">The download sample provides a basic app for testing areas.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="8befc-119">Oblasti pro kontrolery se zobrazeními</span><span class="sxs-lookup"><span data-stu-id="8befc-119">Areas for controllers with views</span></span>

<span data-ttu-id="8befc-120">Typické webové aplikace ASP.NET Core pomocí oblastí, kontrolerů a zobrazení obsahuje následující prvky:</span><span class="sxs-lookup"><span data-stu-id="8befc-120">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="8befc-121">[Strukturu složek oblasti](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="8befc-121">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="8befc-122">Kontrolery upravené pomocí [ &lbrack;oblasti&rbrack; ](#attribute) atribut asociovat řadič se serverem oblasti: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="8befc-122">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span></span>
* <span data-ttu-id="8befc-123">[Oblasti trasa přidaná do spuštění](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span><span class="sxs-lookup"><span data-stu-id="8befc-123">The [area route added to startup](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span></span>

## <a name="area-folder-structure"></a><span data-ttu-id="8befc-124">Struktura složek oblasti</span><span class="sxs-lookup"><span data-stu-id="8befc-124">Area folder structure</span></span>
<span data-ttu-id="8befc-125">Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*.</span><span class="sxs-lookup"><span data-stu-id="8befc-125">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="8befc-126">Používat, bude podobný následujícímu strukturu složek:</span><span class="sxs-lookup"><span data-stu-id="8befc-126">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="8befc-127">Název projektu</span><span class="sxs-lookup"><span data-stu-id="8befc-127">Project name</span></span>
  * <span data-ttu-id="8befc-128">Oblasti</span><span class="sxs-lookup"><span data-stu-id="8befc-128">Areas</span></span>
    * <span data-ttu-id="8befc-129">Produkty</span><span class="sxs-lookup"><span data-stu-id="8befc-129">Products</span></span>
      * <span data-ttu-id="8befc-130">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="8befc-130">Controllers</span></span>
        * <span data-ttu-id="8befc-131">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8befc-131">HomeController.cs</span></span>
        * <span data-ttu-id="8befc-132">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="8befc-132">ManageController.cs</span></span>
      * <span data-ttu-id="8befc-133">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="8befc-133">Views</span></span>
        * <span data-ttu-id="8befc-134">Domů</span><span class="sxs-lookup"><span data-stu-id="8befc-134">Home</span></span>
          * <span data-ttu-id="8befc-135">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8befc-135">Index.cshtml</span></span>
        * <span data-ttu-id="8befc-136">Správa</span><span class="sxs-lookup"><span data-stu-id="8befc-136">Manage</span></span>
          * <span data-ttu-id="8befc-137">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8befc-137">Index.cshtml</span></span>
          * <span data-ttu-id="8befc-138">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="8befc-138">About.cshtml</span></span>
    * <span data-ttu-id="8befc-139">Služby</span><span class="sxs-lookup"><span data-stu-id="8befc-139">Services</span></span>
      * <span data-ttu-id="8befc-140">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="8befc-140">Controllers</span></span>
        * <span data-ttu-id="8befc-141">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="8befc-141">HomeController.cs</span></span>
      * <span data-ttu-id="8befc-142">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="8befc-142">Views</span></span>
        * <span data-ttu-id="8befc-143">Domů</span><span class="sxs-lookup"><span data-stu-id="8befc-143">Home</span></span>
          * <span data-ttu-id="8befc-144">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="8befc-144">Index.cshtml</span></span>

<span data-ttu-id="8befc-145">Při předchozím rozložení je obvyklá, když pomocí oblastí, zobrazit soubory je potřeba použít tuto strukturu složek.</span><span class="sxs-lookup"><span data-stu-id="8befc-145">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="8befc-146">Zobrazení zjišťování vyhledá odpovídající oblast zobrazení soubor v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="8befc-146">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="8befc-147">Umístění složky jiných zobrazení, jako jsou *řadiče* a *modely* nemá **není** záleží.</span><span class="sxs-lookup"><span data-stu-id="8befc-147">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="8befc-148">Například *řadiče* a *modely* složky nejsou povinné.</span><span class="sxs-lookup"><span data-stu-id="8befc-148">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="8befc-149">Obsah *řadiče* a *modely* je kód, který získá kompilovány do knihovny DLL.</span><span class="sxs-lookup"><span data-stu-id="8befc-149">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="8befc-150">Obsah *zobrazení* není kompilována, dokud byla podána žádost do tohoto zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8befc-150">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<!-- TODO review:
The content of the *Views* isn't compiled until a request to that view has been made.

What about precompiled views? 
 -->
<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="8befc-151">Asociovat řadič se serverem oblast</span><span class="sxs-lookup"><span data-stu-id="8befc-151">Associate the controller with an Area</span></span>

<span data-ttu-id="8befc-152">Oblast řadiče jsou označeny [ &lbrack;oblasti&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) atribut:</span><span class="sxs-lookup"><span data-stu-id="8befc-152">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="8befc-153">Přidat oblast směrování</span><span class="sxs-lookup"><span data-stu-id="8befc-153">Add Area route</span></span>

<span data-ttu-id="8befc-154">Cesty oblasti obvykle používají konvenční směrování místo směrováním atributů.</span><span class="sxs-lookup"><span data-stu-id="8befc-154">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="8befc-155">Konvenční směrování je závislé na pořadí.</span><span class="sxs-lookup"><span data-stu-id="8befc-155">Conventional routing is order-dependent.</span></span> <span data-ttu-id="8befc-156">Obecně platí trasy s oblastmi by měl umístit výše ve směrovací tabulce, jako jsou podrobnější než trasy bez oblasti.</span><span class="sxs-lookup"><span data-stu-id="8befc-156">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="8befc-157">`{area:...}` slouží jako token v šablonách tras Pokud ve všech oblastech je jednotné místo adresy url:</span><span class="sxs-lookup"><span data-stu-id="8befc-157">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="8befc-158">V předchozím kódu `exists` platí omezení, že postupu musí odpovídat oblast.</span><span class="sxs-lookup"><span data-stu-id="8befc-158">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="8befc-159">Pomocí `{area:...}` je nejjednodušším mechanismus pro přidání směrování do oblasti.</span><span class="sxs-lookup"><span data-stu-id="8befc-159">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="8befc-160">Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> vytvořit dva pojmenované oblasti trasy:</span><span class="sxs-lookup"><span data-stu-id="8befc-160">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="8befc-161">Při použití `MapAreaRoute` 2.2 technologie ASP.NET Core, najdete v článku [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="8befc-161">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="8befc-162">Další informace najdete v tématu [oblasti směrování](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="8befc-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-areas"></a><span data-ttu-id="8befc-163">Generování odkazů s oblastmi</span><span class="sxs-lookup"><span data-stu-id="8befc-163">Link Generation with Areas</span></span>

<span data-ttu-id="8befc-164">Následující kód z [vzorku ke stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje odkaz generace k zadané oblasti:</span><span class="sxs-lookup"><span data-stu-id="8befc-164">The following code from the [sample download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="8befc-165">Odkazy vygeneroval s předchozím kódu jsou platné kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8befc-165">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="8befc-166">Zahrnuje vzorku ke stažení [částečné zobrazení](xref:mvc/views/partial) bez zadání oblasti, která obsahuje výše uvedené odkazy a stejné odkazy.</span><span class="sxs-lookup"><span data-stu-id="8befc-166">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="8befc-167">Částečné zobrazení odkazuje [soubor rozložení](), takže každé stránky v aplikaci zobrazí vygenerovaný odkazy.</span><span class="sxs-lookup"><span data-stu-id="8befc-167">The partial view is referenced in the [layout file](), so every page in the app displays the generated links.</span></span> <span data-ttu-id="8befc-168">Odkazy generované bez zadání oblasti platí pouze při odkazování z stránku ve stejné oblasti a kontroler.</span><span class="sxs-lookup"><span data-stu-id="8befc-168">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="8befc-169">Pokud oblast nebo kontroler není zadána, směrování závisí *okolí* hodnoty.</span><span class="sxs-lookup"><span data-stu-id="8befc-169">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="8befc-170">Aktuální hodnoty trasy z aktuální požadavek jsou považovány za okolí hodnoty pro generování odkazů.</span><span class="sxs-lookup"><span data-stu-id="8befc-170">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="8befc-171">V mnoha případech pro ukázkovou aplikaci pomocí okolí hodnot generuje nesprávné odkazy.</span><span class="sxs-lookup"><span data-stu-id="8befc-171">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="8befc-172">Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="8befc-172">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="8befc-173">Sdílené rozložení pro použití souboru soubor _ViewStart.cshtml oblasti</span><span class="sxs-lookup"><span data-stu-id="8befc-173">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="8befc-174">Chcete-li sdílet společný rozložení pro celé aplikace, přesuňte *soubor _ViewStart.cshtml* ke kořenové složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="8befc-174">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<!-- This section will be completed after https://github.com/aspnet/Docs/pull/10978 is merged.
<a name="arp"></a>

## Areas for Razor Pages
-->
<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="8befc-175">Změna výchozí oblast složky pro uložení zobrazení</span><span class="sxs-lookup"><span data-stu-id="8befc-175">Change default area folder where views are stored</span></span>

<span data-ttu-id="8befc-176">Následující kód změní výchozí oblast složku z `"Areas"` k `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="8befc-176">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<!-- TODO review - can we delete this. Areas doesn't change publishing - right? -->
### <a name="publishing-areas"></a><span data-ttu-id="8befc-177">Publikování oblasti</span><span class="sxs-lookup"><span data-stu-id="8befc-177">Publishing Areas</span></span>

<span data-ttu-id="8befc-178">Všechny `*.cshtml` a `wwwroot/**` souborů k publikování do výstupu, kdy `<Project Sdk="Microsoft.NET.Sdk.Web">` je součástí *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="8befc-178">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>
