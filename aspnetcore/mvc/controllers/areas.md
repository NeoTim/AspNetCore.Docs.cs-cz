---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak oblasti jsou používány pro organizaci související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení) funkce služby technologie ASP.NET MVC.
ms.author: riande
ms.date: 02/14/2017
uid: mvc/controllers/areas
ms.openlocfilehash: 19e818fa198936ea1bee0da8039e88a3c0abbf6b
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410609"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="ae2f7-103">Oblasti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae2f7-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="ae2f7-104">Podle [Dhananjay Kumar](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ae2f7-104">By [Dhananjay Kumar](https://twitter.com/debug_mode)  and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ae2f7-105">Oblasti jsou používány pro organizaci související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení) funkce služby technologie ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-105">Areas are an ASP.NET MVC feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="ae2f7-106">Pomocí oblastí vytvoří hierarchii pro účely směrování tak, že přidáte další parametr trasy, `area`do `controller` a `action`.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action`.</span></span>

<span data-ttu-id="ae2f7-107">Oblasti poskytují způsob, jak rozdělit velké aplikace ASP.NET Core MVC Web na menší funkční seskupení.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-107">Areas provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="ae2f7-108">Oblast je v podstatě struktury MVC uvnitř aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-108">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="ae2f7-109">V projektu aplikace MVC logické komponenty, jako jsou modelu, Kontroleru a zobrazení jsou uloženy v různých složkách a aplikace MVC používá konvence pojmenování a vytvořit tak relaci mezi těmito součástmi.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-109">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="ae2f7-110">Pro velké aplikace může být výhodné rozdělit na samostatné vysoké úrovně funkční oblasti aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-110">For a large app, it may be advantageous to partition the  app into separate high level areas of functionality.</span></span> <span data-ttu-id="ae2f7-111">Například e-commerce aplikace s několika obchodními jednotkami, jako je například checkout, fakturace a vyhledávání atd. Každá z těchto jednotek mít své vlastní logickou součástí zobrazení, kontrolerů a modely.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-111">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span> <span data-ttu-id="ae2f7-112">V tomto scénáři použijete k oblasti fyzicky rozdělení komponent firmy ve stejném projektu.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-112">In this scenario, you can use Areas to physically partition the business components in the same project.</span></span>

<span data-ttu-id="ae2f7-113">Oblast je definovat jako menších funkční jednotek v projektu aplikace ASP.NET Core MVC s vlastní sadou řadiče, zobrazení a modely.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-113">An area can be defined as smaller functional units in an ASP.NET Core MVC project with its own set of controllers, views, and models.</span></span>

<span data-ttu-id="ae2f7-114">Zvažte použití oblastí v MVC projektu při:</span><span class="sxs-lookup"><span data-stu-id="ae2f7-114">Consider using Areas in an MVC project when:</span></span>

* <span data-ttu-id="ae2f7-115">Vaše aplikace je proveden součástí více vysoké úrovně funkčnosti, která by měla být oddělena logicky</span><span class="sxs-lookup"><span data-stu-id="ae2f7-115">Your application is made of multiple high-level functional components that should be logically separated</span></span>

* <span data-ttu-id="ae2f7-116">Chcete rozdělit projekt MVC tak, aby každá funkční oblast je možné pracovat nezávisle na sobě</span><span class="sxs-lookup"><span data-stu-id="ae2f7-116">You want to partition your MVC project so that each functional area can be worked on independently</span></span>

<span data-ttu-id="ae2f7-117">Oblasti funkce:</span><span class="sxs-lookup"><span data-stu-id="ae2f7-117">Area features:</span></span>

* <span data-ttu-id="ae2f7-118">Aplikace ASP.NET Core MVC může mít libovolný počet oblastí.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-118">An ASP.NET Core MVC app can have any number of areas.</span></span>

* <span data-ttu-id="ae2f7-119">Každá oblast má vlastní řadiče, modely a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-119">Each area has its own controllers, models, and views.</span></span>

* <span data-ttu-id="ae2f7-120">Oblasti umožňují organizovat velké projekty MVC do více základní součásti, které je možné pracovat nezávisle na sobě.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-120">Areas allow you to organize large MVC projects into multiple high-level components that can be worked on independently.</span></span>

* <span data-ttu-id="ae2f7-121">Oblasti podporují víc řadičích se stejným názvem, za předpokladu, že mají různé *oblasti*.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-121">Areas support multiple controllers with the same name, as long as they have different *areas*.</span></span>

<span data-ttu-id="ae2f7-122">Pojďme se podívat na příklad, který ukazuje, jak vytvořit a použít oblasti.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-122">Let's take a look at an example to illustrate how Areas are created and used.</span></span> <span data-ttu-id="ae2f7-123">Řekněme, že máte aplikace ze storu, která má dvě odlišné skupiny kontrolerů a zobrazení: Produkty a služby.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-123">Let's say you have a store app that has two distinct groupings of controllers and views: Products and Services.</span></span> <span data-ttu-id="ae2f7-124">Složka je obvykle strukturu pro, používat MVC vypadá níže:</span><span class="sxs-lookup"><span data-stu-id="ae2f7-124">A typical folder structure for that using MVC areas looks like below:</span></span>

* <span data-ttu-id="ae2f7-125">Název projektu</span><span class="sxs-lookup"><span data-stu-id="ae2f7-125">Project name</span></span>
  * <span data-ttu-id="ae2f7-126">Oblasti</span><span class="sxs-lookup"><span data-stu-id="ae2f7-126">Areas</span></span>
    * <span data-ttu-id="ae2f7-127">Produkty</span><span class="sxs-lookup"><span data-stu-id="ae2f7-127">Products</span></span>
      * <span data-ttu-id="ae2f7-128">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="ae2f7-128">Controllers</span></span>
        * <span data-ttu-id="ae2f7-129">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="ae2f7-129">HomeController.cs</span></span>
        * <span data-ttu-id="ae2f7-130">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="ae2f7-130">ManageController.cs</span></span>
      * <span data-ttu-id="ae2f7-131">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="ae2f7-131">Views</span></span>
        * <span data-ttu-id="ae2f7-132">Domů</span><span class="sxs-lookup"><span data-stu-id="ae2f7-132">Home</span></span>
          * <span data-ttu-id="ae2f7-133">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ae2f7-133">Index.cshtml</span></span>
        * <span data-ttu-id="ae2f7-134">Správa</span><span class="sxs-lookup"><span data-stu-id="ae2f7-134">Manage</span></span>
          * <span data-ttu-id="ae2f7-135">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ae2f7-135">Index.cshtml</span></span>
    * <span data-ttu-id="ae2f7-136">Služby</span><span class="sxs-lookup"><span data-stu-id="ae2f7-136">Services</span></span>
      * <span data-ttu-id="ae2f7-137">Kontrolery</span><span class="sxs-lookup"><span data-stu-id="ae2f7-137">Controllers</span></span>
        * <span data-ttu-id="ae2f7-138">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="ae2f7-138">HomeController.cs</span></span>
      * <span data-ttu-id="ae2f7-139">Zobrazení</span><span class="sxs-lookup"><span data-stu-id="ae2f7-139">Views</span></span>
        * <span data-ttu-id="ae2f7-140">Domů</span><span class="sxs-lookup"><span data-stu-id="ae2f7-140">Home</span></span>
          * <span data-ttu-id="ae2f7-141">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ae2f7-141">Index.cshtml</span></span>

<span data-ttu-id="ae2f7-142">MVC se pokusí pro vykreslení zobrazení v oblasti, ve výchozím nastavení, pokusí se vás pod rouškou v následujících umístěních:</span><span class="sxs-lookup"><span data-stu-id="ae2f7-142">When MVC tries to render a view in an Area, by default, it tries to look in the following locations:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="ae2f7-143">Toto jsou výchozí umístění, které lze změnit prostřednictvím `AreaViewLocationFormats` na `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-143">These are the default locations which can be changed via the `AreaViewLocationFormats` on the `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span></span>

<span data-ttu-id="ae2f7-144">Například v níže uvedeného kódu namísto nutnosti název složky jako "Oblasti", byl změněn na "Kategorie".</span><span class="sxs-lookup"><span data-stu-id="ae2f7-144">For example, in the below code instead of having the folder name as 'Areas', it has been changed to 'Categories'.</span></span>

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

<span data-ttu-id="ae2f7-145">Poznámka je struktura *zobrazení* složky je pouze jeden, který je považován za důležité tady a obsah ostatních složek, jako jsou *řadiče* a *modely* nemá **není** záleží.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-145">One thing to note is that the structure of the *Views* folder is the only one which is considered important here and the content of the rest of the folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="ae2f7-146">Například nemusí mít *řadiče* a *modely* složky vůbec.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-146">For example, you need not have a *Controllers* and *Models* folder at all.</span></span> <span data-ttu-id="ae2f7-147">Tento postup funguje, protože obsah *řadiče* a *modely* je jenom kód, který získá kompilovány do knihovny DLL tam, kde jako obsah *zobrazení* není až do požadavek, který zobrazení byly provedeny.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-147">This works because the content of *Controllers* and *Models* is just code which gets compiled into a .dll where as the content of the *Views* isn't until a request to that view has been made.</span></span>

<span data-ttu-id="ae2f7-148">Jakmile jste definovali hierarchii složek, je třeba sdělit MVC, že je každý kontroler přidružené k oblasti.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-148">Once you've defined the folder hierarchy, you need to tell MVC that each controller is associated with an area.</span></span> <span data-ttu-id="ae2f7-149">Můžete to udělat pomocí upravení názvu kontroleru se `[Area]` atribut.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-149">You do that by decorating the controller name with the `[Area]` attribute.</span></span>

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

<span data-ttu-id="ae2f7-150">Nastavte definici trasy, která funguje s nově vytvořený oblasti.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-150">Set up a route definition that works with your newly created areas.</span></span> <span data-ttu-id="ae2f7-151">[Trasy na akce kontroleru](routing.md) článek obsahuje podrobnosti o tom, jak vytvořit trasu definic, včetně použití konvenční trasy a trasy atributů.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-151">The [Route to controller actions](routing.md) article goes into detail about how to create route definitions, including using conventional routes versus attribute routes.</span></span> <span data-ttu-id="ae2f7-152">V tomto příkladu použijeme konvenční trasy.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-152">In this example, we'll use a conventional route.</span></span> <span data-ttu-id="ae2f7-153">Chcete-li to provést, otevřete *Startup.cs* soubor a upravit ji tak, že přidáte `areaRoute` s názvem definice trasy níže.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-153">To do so, open the *Startup.cs* file and modify it by adding the `areaRoute` named route definition below.</span></span>

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(
         name: "areaRoute",
         template: "{area:exists}/{controller=Home}/{action=Index}/{id?}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

<span data-ttu-id="ae2f7-154">Přejdete na adresu `http://<yourApp>/products`, `Index` metody akce `HomeController` v `Products` oblasti, který bude vyvolán.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-154">Browsing to `http://<yourApp>/products`, the `Index` action method of the `HomeController` in the `Products` area will be invoked.</span></span>

## <a name="link-generation"></a><span data-ttu-id="ae2f7-155">Generování odkazů</span><span class="sxs-lookup"><span data-stu-id="ae2f7-155">Link Generation</span></span>

* <span data-ttu-id="ae2f7-156">Generování odkazů z akce v rámci oblasti na základě kontroleru další akce v rámci stejné kontroleru.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-156">Generating links from an action within an area based controller to another action within the same controller.</span></span>

  <span data-ttu-id="ae2f7-157">Řekněme, že je cesta aktuální žádosti jako `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-157">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="ae2f7-158">HtmlHelper syntaxe: `@Html.ActionLink("Go to Product's Home Page", "Index")`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-158">HtmlHelper syntax: `@Html.ActionLink("Go to Product's Home Page", "Index")`</span></span>

  <span data-ttu-id="ae2f7-159">Taghelperu. syntaxe: `<a asp-action="Index">Go to Product's Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-159">TagHelper syntax: `<a asp-action="Index">Go to Product's Home Page</a>`</span></span>

  <span data-ttu-id="ae2f7-160">Všimněte si, že jsme nemusí poskytnout hodnoty "oblasti" a "controller" Zde jsou už k dispozici v rámci aktuálního požadavku.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-160">Note that we need not supply the 'area' and 'controller' values here as they're already available in the context of the current request.</span></span> <span data-ttu-id="ae2f7-161">Tento druh hodnoty se nazývají `ambient` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-161">These kind of values are called `ambient` values.</span></span>

* <span data-ttu-id="ae2f7-162">Generování odkazů z akce v rámci oblasti na základě kontroleru další akci na jiný kontroler</span><span class="sxs-lookup"><span data-stu-id="ae2f7-162">Generating links from an action within an area based controller to another action on a different controller</span></span>

  <span data-ttu-id="ae2f7-163">Řekněme, že je cesta aktuální žádosti jako `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-163">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="ae2f7-164">HtmlHelper syntaxe: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-164">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`</span></span>

  <span data-ttu-id="ae2f7-165">Taghelperu. syntaxe: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-165">TagHelper syntax: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span></span>

  <span data-ttu-id="ae2f7-166">Všimněte si, že tady se používá okolí hodnotu "oblasti", ale explicitně zadaná hodnota 'controller' výše.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-166">Note that here the ambient value of an 'area' is used but the 'controller' value is specified explicitly above.</span></span>

* <span data-ttu-id="ae2f7-167">Generování odkazů z akce v rámci oblasti kontroleru další akci na základě různých kontroleru a do jiné oblasti.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-167">Generating links from an action within an area based controller to another action on a different controller and a different area.</span></span>

  <span data-ttu-id="ae2f7-168">Řekněme, že je cesta aktuální žádosti jako `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-168">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="ae2f7-169">HtmlHelper syntaxe: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-169">HtmlHelper syntax: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`</span></span>

  <span data-ttu-id="ae2f7-170">Taghelperu. syntaxe: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-170">TagHelper syntax: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`</span></span>

  <span data-ttu-id="ae2f7-171">Všimněte si, že tady žádné okolí hodnoty jsou použity.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-171">Note that here no ambient values are used.</span></span>

* <span data-ttu-id="ae2f7-172">Generování odkazů z akce v rámci řadič oblasti založené na jinou akci na jiný kontroler a **není** v oblasti.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-172">Generating links from an action within an area based controller to another action on a different controller and **not** in an area.</span></span>

  <span data-ttu-id="ae2f7-173">HtmlHelper syntaxe: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-173">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`</span></span>

  <span data-ttu-id="ae2f7-174">Taghelperu. syntaxe: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="ae2f7-174">TagHelper syntax: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span></span>

  <span data-ttu-id="ae2f7-175">Protože chceme, aby ke generování odkazů do jiné oblasti na základě akce kontroleru, jsme prázdný okolí hodnotu "oblasti" zde.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-175">Since we want to generate links to a non-area based controller action, we empty the ambient value for 'area' here.</span></span>

## <a name="publishing-areas"></a><span data-ttu-id="ae2f7-176">Publikování oblasti</span><span class="sxs-lookup"><span data-stu-id="ae2f7-176">Publishing Areas</span></span>

<span data-ttu-id="ae2f7-177">Všechny `*.cshtml` a `wwwroot/**` souborů k publikování do výstupu, kdy `<Project Sdk="Microsoft.NET.Sdk.Web">` je součástí *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="ae2f7-177">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>
