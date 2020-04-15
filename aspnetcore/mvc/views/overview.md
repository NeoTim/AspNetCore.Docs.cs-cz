---
title: Pohledy v ASP.NET Jádra MVC
author: ardalis
description: Zjistěte, jak zobrazení zvládnou prezentaci dat aplikace a interakci s uživatelem v ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/overview
ms.openlocfilehash: 70b8c2c01a28f99dd384351041a3b77d23f46a48
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384072"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="04b7a-103">Pohledy v ASP.NET Jádra MVC</span><span class="sxs-lookup"><span data-stu-id="04b7a-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="04b7a-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="04b7a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="04b7a-105">Tento dokument vysvětluje zobrazení používaná v aplikacích Core MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="04b7a-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="04b7a-106">Informace o stránkách Razor naleznete v [tématu Úvod do stránek Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="04b7a-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="04b7a-107">Ve vzoru Model-View-Controller (MVC) *zobrazení* zpracovává prezentaci dat aplikace a interakci s uživatelem.</span><span class="sxs-lookup"><span data-stu-id="04b7a-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="04b7a-108">Zobrazení je šablona HTML s vloženými [značkami Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="04b7a-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="04b7a-109">Razor značky je kód, který spolupracuje se značkami HTML k vytvoření webové stránky, která je odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="04b7a-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="04b7a-110">V ASP.NET Core MVC jsou zobrazení *.cshtml* soubory, které používají [programovací jazyk C#](/dotnet/csharp/) v razor značky.</span><span class="sxs-lookup"><span data-stu-id="04b7a-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="04b7a-111">Soubory zobrazení jsou obvykle seskupeny do složek pojmenovaných pro každý řadič aplikace [.](xref:mvc/controllers/actions)</span><span class="sxs-lookup"><span data-stu-id="04b7a-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="04b7a-112">Složky jsou uloženy ve složce *Zobrazení* v kořenovém adresáři aplikace:</span><span class="sxs-lookup"><span data-stu-id="04b7a-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Složka Zobrazení v Průzkumníku řešení sady Visual Studio je otevřená s otevřenou složky Domů, která zobrazuje soubory About.cshtml, Contact.cshtml a Index.cshtml](overview/_static/views_solution_explorer.png)

<span data-ttu-id="04b7a-114">Domácí *Home* ovladač je reprezentován složky *Home* uvnitř složky *Zobrazení.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="04b7a-115">Složka *Domů* obsahuje zobrazení webových stránek *O* *aplikaci*, Kontakt a *Rejstřík* (domovská stránka).</span><span class="sxs-lookup"><span data-stu-id="04b7a-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="04b7a-116">Když uživatel požádá o jednu z těchto tří webových stránek, akce kontroleru v *řadiči plochy* určují, které ze tří zobrazení se používá k vytvoření a vrácení webové stránky uživateli.</span><span class="sxs-lookup"><span data-stu-id="04b7a-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="04b7a-117">Rozložení [layouts](xref:mvc/views/layout) slouží k zajištění konzistentních oddílů webových stránek a snížení opakování kódu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="04b7a-118">Rozložení často obsahují záhlaví, navigaci a prvky nabídky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="04b7a-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="04b7a-119">Záhlaví a zápatí obvykle obsahují standardní značky pro mnoho prvků metadat a odkazy na prostředky skriptu a stylu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="04b7a-120">Rozložení vám pomohou vyhnout se této standardní značky v zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="04b7a-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="04b7a-121">[Částečná zobrazení](xref:mvc/views/partial) snižují duplikaci kódu správou opakovaně použitelných částí zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="04b7a-122">Částečné zobrazení je například užitečné pro biografii autora na webu blogu, který se zobrazuje v několika zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="04b7a-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="04b7a-123">Životopis autora je běžný obsah zobrazení a nevyžaduje spuštění kódu, aby bylo možné vytvořit obsah pro webovou stránku.</span><span class="sxs-lookup"><span data-stu-id="04b7a-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="04b7a-124">Obsah životopisu autora je k dispozici pohledu pouze pomocí vazby modelu, takže použití částečného zobrazení pro tento typ obsahu je ideální.</span><span class="sxs-lookup"><span data-stu-id="04b7a-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="04b7a-125">[Komponenty zobrazení](xref:mvc/views/view-components) jsou podobné částečným zobrazením v tom, že umožňují snížit opakující se kód, ale jsou vhodné pro zobrazení obsahu, který vyžaduje spuštění kódu na serveru za účelem vykreslení webové stránky.</span><span class="sxs-lookup"><span data-stu-id="04b7a-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="04b7a-126">Komponenty zobrazení jsou užitečné, když vykreslený obsah vyžaduje interakci s databází, například pro nákupní košík webu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="04b7a-127">Součásti zobrazení nejsou omezeny na vazbu modelu za účelem vytvoření výstupu webové stránky.</span><span class="sxs-lookup"><span data-stu-id="04b7a-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="04b7a-128">Výhody použití zobrazení</span><span class="sxs-lookup"><span data-stu-id="04b7a-128">Benefits of using views</span></span>

<span data-ttu-id="04b7a-129">Zobrazení pomáhají vytvořit [oddělení problémů](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) v rámci aplikace MVC oddělením značek uživatelského rozhraní od jiných částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b7a-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="04b7a-130">Následující soc design dělá vaši aplikaci modulární, což poskytuje několik výhod:</span><span class="sxs-lookup"><span data-stu-id="04b7a-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="04b7a-131">Aplikace se snadněji udržuje, protože je lépe organizovaná.</span><span class="sxs-lookup"><span data-stu-id="04b7a-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="04b7a-132">Zobrazení jsou obvykle seskupena podle funkce aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b7a-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="04b7a-133">To usnadňuje vyhledání souvisejících zobrazení při práci na prvku.</span><span class="sxs-lookup"><span data-stu-id="04b7a-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="04b7a-134">Části aplikace jsou volně spojeny.</span><span class="sxs-lookup"><span data-stu-id="04b7a-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="04b7a-135">Zobrazení aplikace můžete vytvářet a aktualizovat odděleně od obchodní logiky a součástí přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="04b7a-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="04b7a-136">Můžete upravit zobrazení aplikace, aniž byste museli aktualizovat další části aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b7a-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="04b7a-137">Je jednodušší otestovat části uživatelského rozhraní aplikace, protože zobrazení jsou samostatné jednotky.</span><span class="sxs-lookup"><span data-stu-id="04b7a-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="04b7a-138">Vzhledem k lepší organizaci je méně pravděpodobné, že budete náhodně opakovat části uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04b7a-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="04b7a-139">Vytvoření zobrazení</span><span class="sxs-lookup"><span data-stu-id="04b7a-139">Creating a view</span></span>

<span data-ttu-id="04b7a-140">Zobrazení, která jsou specifická pro řadič, jsou vytvořena ve složce *Zobrazení/[ControllerName].*</span><span class="sxs-lookup"><span data-stu-id="04b7a-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="04b7a-141">Zobrazení, která jsou sdílena mezi řadiči, jsou umístěna ve složce *Zobrazení nebo Sdílené.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="04b7a-142">Chcete-li vytvořit zobrazení, přidejte nový soubor a přiřazujte mu stejný název jako jeho přidružená akce řadiče s příponou *souboru .cshtml.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="04b7a-143">Chcete-li vytvořit zobrazení, které odpovídá akci *Informace* v řadiči *Domů,* vytvořte soubor *About.cshtml* ve složce *Zobrazení/Domov:*</span><span class="sxs-lookup"><span data-stu-id="04b7a-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="04b7a-144">*Značka holicího strojku* začíná symbolem. `@`</span><span class="sxs-lookup"><span data-stu-id="04b7a-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="04b7a-145">Spusťte příkazy Jazyka C# umístěním kódu Jazyka C#`{ ... }`do bloků kódu [Razor,](xref:mvc/views/razor#razor-code-blocks) které jsou nastaveny složenými závorkami ( ).</span><span class="sxs-lookup"><span data-stu-id="04b7a-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="04b7a-146">Podívejte se například na přiřazení `ViewData["Title"]` "O" na obrázku výše.</span><span class="sxs-lookup"><span data-stu-id="04b7a-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="04b7a-147">Hodnoty v html můžete zobrazit tak, že `@` jednoduše na hodnotu odkazujete se symbolem.</span><span class="sxs-lookup"><span data-stu-id="04b7a-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="04b7a-148">Podívejte se na `<h2>` `<h3>` obsah a prvky výše.</span><span class="sxs-lookup"><span data-stu-id="04b7a-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="04b7a-149">Obsah zobrazení zobrazený výše je pouze částí celé webové stránky, která je vykreslována uživateli.</span><span class="sxs-lookup"><span data-stu-id="04b7a-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="04b7a-150">Zbývající rozložení stránky a další běžné aspekty zobrazení jsou určeny v jiných souborech zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="04b7a-151">Další informace naleznete v [tématu Rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="04b7a-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="04b7a-152">Jak kontrolidy určují zobrazení</span><span class="sxs-lookup"><span data-stu-id="04b7a-152">How controllers specify views</span></span>

<span data-ttu-id="04b7a-153">Zobrazení jsou obvykle vráceny z akcí jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), což je typ [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="04b7a-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="04b7a-154">Vaše metoda akce můžete `ViewResult` vytvořit a vrátit přímo, ale to se běžně neprovádí.</span><span class="sxs-lookup"><span data-stu-id="04b7a-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="04b7a-155">Vzhledem k tomu, že většina `View` řadičů dědí `ViewResult`z [řadiče](/dotnet/api/microsoft.aspnetcore.mvc.controller), jednoduše použijte pomocnou metodu k vrácení :</span><span class="sxs-lookup"><span data-stu-id="04b7a-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="04b7a-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="04b7a-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="04b7a-157">Když se tato akce vrátí, zobrazení *About.cshtml* zobrazené v poslední části se vykreslí jako následující webová stránka:</span><span class="sxs-lookup"><span data-stu-id="04b7a-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![O stránce vykreslené v prohlížeči Edge](overview/_static/about-page.png)

<span data-ttu-id="04b7a-159">`View` Pomocná metoda má několik přetížení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="04b7a-160">Volitelně můžete zadat:</span><span class="sxs-lookup"><span data-stu-id="04b7a-160">You can optionally specify:</span></span>

* <span data-ttu-id="04b7a-161">Explicitní zobrazení pro vrácení:</span><span class="sxs-lookup"><span data-stu-id="04b7a-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="04b7a-162">[Model,](xref:mvc/models/model-binding) který má být předáván do pohledu:</span><span class="sxs-lookup"><span data-stu-id="04b7a-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="04b7a-163">Pohled i model:</span><span class="sxs-lookup"><span data-stu-id="04b7a-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="04b7a-164">Zobrazit zjišťování</span><span class="sxs-lookup"><span data-stu-id="04b7a-164">View discovery</span></span>

<span data-ttu-id="04b7a-165">Když akce vrátí zobrazení, probíhá proces nazývaný *zjišťování zobrazení.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="04b7a-166">Tento proces určuje, který soubor zobrazení se používá na základě názvu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="04b7a-167">Výchozí chování `View` metody (`return View();`) je vrátit zobrazení se stejným názvem jako metoda akce, ze které je volána.</span><span class="sxs-lookup"><span data-stu-id="04b7a-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="04b7a-168">Název metody *About* `ActionResult` řadiče se například používá k vyhledání souboru zobrazení s názvem *About.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="04b7a-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="04b7a-169">Nejprve se běhový čas podívá do složky *Zobrazení/[Název_celého_zařízení]* pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="04b7a-170">Pokud tam nenajde odpovídající zobrazení, prohledá *sdílenou* složku pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="04b7a-171">Nezáleží na tom, zda implicitně vrátíte `ViewResult` s `return View();` nebo `View` explicitně `return View("<ViewName>");`předat název zobrazení metodě s .</span><span class="sxs-lookup"><span data-stu-id="04b7a-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="04b7a-172">V obou případech zobrazte zjišťování, které hledá odpovídající soubor zobrazení v tomto pořadí:</span><span class="sxs-lookup"><span data-stu-id="04b7a-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="04b7a-173">*Zobrazení/\[Název_řadiče]/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="04b7a-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="04b7a-174">*Zobrazení/Sdílené/\[ViewName].cshtml*</span><span class="sxs-lookup"><span data-stu-id="04b7a-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="04b7a-175">Místo názvu zobrazení lze poskytnout cestu k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="04b7a-176">Pokud používáte absolutní cestu začínající v kořenovém adresáři aplikace (volitelně začínající na "/" nebo "~/"), musí být zadáno rozšíření *.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="04b7a-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="04b7a-177">Relativní cestu můžete také použít k určení zobrazení v různých adresářích bez rozšíření *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="04b7a-178">Uvnitř `HomeController`aplikace můžete vrátit zobrazení *indexu* zobrazení *Správa* zobrazení s relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="04b7a-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="04b7a-179">Podobně můžete označit aktuální adresář specifický pro řadič s předponou "./":</span><span class="sxs-lookup"><span data-stu-id="04b7a-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="04b7a-180">[Částečné pohledy](xref:mvc/views/partial) a [součásti zobrazení](xref:mvc/views/view-components) používají podobné (ale ne identické) mechanismy zjišťování.</span><span class="sxs-lookup"><span data-stu-id="04b7a-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="04b7a-181">Výchozí konvenci pro umístění zobrazení v aplikaci můžete přizpůsobit pomocí vlastního [iViewLocationExpanderu](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span><span class="sxs-lookup"><span data-stu-id="04b7a-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="04b7a-182">Zjišťování zobrazení závisí na hledání souborů zobrazení podle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="04b7a-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="04b7a-183">Pokud základní systém souborů rozlišuje malá a velká písmena, názvy zobrazení jsou pravděpodobně rozlišování velkých a malých písmen.</span><span class="sxs-lookup"><span data-stu-id="04b7a-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="04b7a-184">Z důvodu kompatibility mezi operačními systémy můžete porovnat případ mezi názvy řadiče a akcí a přidruženými složkami zobrazení a názvy souborů.</span><span class="sxs-lookup"><span data-stu-id="04b7a-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="04b7a-185">Pokud narazíte na chybu, že soubor zobrazení nelze nalézt při práci se systémem souborů rozlišující malá a velká písmena, zkontrolujte, zda se velikost písmen shoduje mezi požadovaným souborem zobrazení a skutečným názvem souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="04b7a-186">Postupujte podle osvědčených postupů při uspořádání struktury souborů pro vaše zobrazení tak, aby odrážely vztahy mezi řadiči, akcemi a zobrazeními pro zachování a srozumitelnost.</span><span class="sxs-lookup"><span data-stu-id="04b7a-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="04b7a-187">Předávání dat zobrazením</span><span class="sxs-lookup"><span data-stu-id="04b7a-187">Passing data to views</span></span>

<span data-ttu-id="04b7a-188">Předávat data zobrazením pomocí několika přístupů:</span><span class="sxs-lookup"><span data-stu-id="04b7a-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="04b7a-189">Data silného typu: model zobrazení</span><span class="sxs-lookup"><span data-stu-id="04b7a-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="04b7a-190">Slabě zadaný data</span><span class="sxs-lookup"><span data-stu-id="04b7a-190">Weakly typed data</span></span>
  * <span data-ttu-id="04b7a-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="04b7a-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="04b7a-192">Data silného typu (model zobrazení)</span><span class="sxs-lookup"><span data-stu-id="04b7a-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="04b7a-193">Nejrobustnějším přístupem je určení typu [modelu](xref:mvc/models/model-binding) v pohledu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="04b7a-194">Tento model se běžně označuje jako *model pohledu*.</span><span class="sxs-lookup"><span data-stu-id="04b7a-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="04b7a-195">Instance typu zobrazení modelu zobrazení do pohledu z akce.</span><span class="sxs-lookup"><span data-stu-id="04b7a-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="04b7a-196">Použití modelu zobrazení k předání dat do zobrazení umožňuje zobrazení využít výhod *kontroly silného* typu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="04b7a-197">*Silné psaní* (nebo *silné holčicí typ)* znamená, že každá proměnná a konstanta má explicitně definovaný typ (například `string`, `int`, nebo `DateTime`).</span><span class="sxs-lookup"><span data-stu-id="04b7a-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="04b7a-198">Platnost typů používaných v zobrazení je kontrolována v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="04b7a-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="04b7a-199">[Visual Studio](https://visualstudio.microsoft.com) a [Visual Studio kód](https://code.visualstudio.com/) seznam silně zadali členy třídy pomocí funkce s názvem [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="04b7a-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="04b7a-200">Chcete-li zobrazit vlastnosti modelu pohledu, zadejte název proměnné pro model`.`pohledu následovaný tečkou ( ).</span><span class="sxs-lookup"><span data-stu-id="04b7a-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="04b7a-201">To vám pomůže psát kód rychleji s menším počtem chyb.</span><span class="sxs-lookup"><span data-stu-id="04b7a-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="04b7a-202">Zadejte model `@model` pomocí směrnice.</span><span class="sxs-lookup"><span data-stu-id="04b7a-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="04b7a-203">Model používejte `@Model`s :</span><span class="sxs-lookup"><span data-stu-id="04b7a-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="04b7a-204">Chcete-li poskytnout model pohledu, předává jej řadič jako parametr:</span><span class="sxs-lookup"><span data-stu-id="04b7a-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="04b7a-205">Neexistují žádná omezení pro typy modelů, které můžete poskytnout zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="04b7a-206">Doporučujeme používat plain old CLR object (POCO) viewmodels s malým nebo žádným chováním (metodami) definovanými.</span><span class="sxs-lookup"><span data-stu-id="04b7a-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="04b7a-207">Třídy viewmodel jsou obvykle uloženy ve složce *Modely* nebo samostatné *viewmodels* složky v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="04b7a-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="04b7a-208">Model zobrazení *Adresa* použitý ve výše uvedeném příkladu je model zobrazení POCO uložený v souboru s názvem *Address.cs*:</span><span class="sxs-lookup"><span data-stu-id="04b7a-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="04b7a-209">Nic nebrání použití stejné třídy pro typy zobrazení modelu a typy obchodního modelu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="04b7a-210">Použití samostatných modelů však umožňuje, aby se vaše zobrazení lišila nezávisle na obchodní logice a části aplikace pro přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="04b7a-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="04b7a-211">Oddělení modelů a zobrazení také nabízí výhody zabezpečení, když modely používají [vazbu modelu](xref:mvc/models/model-binding) a [ověřování](xref:mvc/models/validation) dat odeslaných do aplikace uživatelem.</span><span class="sxs-lookup"><span data-stu-id="04b7a-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="04b7a-212">Slabě zadaný data (ViewData, ViewData atribut a ViewBag)</span><span class="sxs-lookup"><span data-stu-id="04b7a-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="04b7a-213">`ViewBag`*není k dispozici v nástroji Razor Pages.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="04b7a-214">Kromě zobrazení silného typu mají zobrazení přístup k *slabě zadanému* (nazývanému také *volně zadaný)* shromažďování dat.</span><span class="sxs-lookup"><span data-stu-id="04b7a-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="04b7a-215">Na rozdíl od silných typů *slabé typy* (nebo *volné typy)* znamená, že není explicitně deklarovat typ dat, které používáte.</span><span class="sxs-lookup"><span data-stu-id="04b7a-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="04b7a-216">Můžete použít shromažďování slabě zadávaných dat pro předávání malých množství dat do a z řadiče a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="04b7a-217">Předávání dat mezi ...</span><span class="sxs-lookup"><span data-stu-id="04b7a-217">Passing data between a ...</span></span>                        | <span data-ttu-id="04b7a-218">Příklad</span><span class="sxs-lookup"><span data-stu-id="04b7a-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="04b7a-219">Řadič a zobrazení</span><span class="sxs-lookup"><span data-stu-id="04b7a-219">Controller and a view</span></span>                             | <span data-ttu-id="04b7a-220">Vyplnění rozevíracího seznamu daty.</span><span class="sxs-lookup"><span data-stu-id="04b7a-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="04b7a-221">Zobrazení a [zobrazení rozložení](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="04b7a-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="04b7a-222">Nastavení \*\* \<názvu>\*\* obsahu prvků v zobrazení rozložení ze souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="04b7a-223">[Částečný pohled](xref:mvc/views/partial) a pohled</span><span class="sxs-lookup"><span data-stu-id="04b7a-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="04b7a-224">Widget, který zobrazuje data na základě webové stránky, kterou uživatel požadoval.</span><span class="sxs-lookup"><span data-stu-id="04b7a-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="04b7a-225">Tato kolekce lze odkazovat `ViewData` prostřednictvím nebo `ViewBag` vlastnosti na řadiče a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="04b7a-226">Vlastnost `ViewData` je slovník slabě zadaných objektů.</span><span class="sxs-lookup"><span data-stu-id="04b7a-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="04b7a-227">Vlastnost `ViewBag` je obálka `ViewData` kolem, který poskytuje `ViewData` dynamické vlastnosti pro základní kolekce.</span><span class="sxs-lookup"><span data-stu-id="04b7a-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="04b7a-228">Poznámka: Hledání klíčů nerozlišují `ViewData` `ViewBag`malá a velká písmena pro obě a .</span><span class="sxs-lookup"><span data-stu-id="04b7a-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="04b7a-229">`ViewData`a `ViewBag` jsou dynamicky vyřešeny za běhu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="04b7a-230">Vzhledem k tomu, že nenabízejí kontrolu typu kompilace, jsou obvykle náchylnější k chybám než použití modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="04b7a-231">Z tohoto důvodu někteří vývojáři dávají `ViewData` přednost `ViewBag`minimálně nebo nikdy použít a .</span><span class="sxs-lookup"><span data-stu-id="04b7a-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="04b7a-232">**Viewdata**</span><span class="sxs-lookup"><span data-stu-id="04b7a-232">**ViewData**</span></span>

<span data-ttu-id="04b7a-233">`ViewData`je objekt [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) přístupný `string` prostřednictvím klíčů.</span><span class="sxs-lookup"><span data-stu-id="04b7a-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="04b7a-234">Řetězcová data mohou být uložena a použita přímo bez `ViewData` nutnosti přetypování, ale při jejich extrahování je nutné přetypovat jiné hodnoty objektů na určité typy.</span><span class="sxs-lookup"><span data-stu-id="04b7a-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="04b7a-235">Data z `ViewData` řadičů můžete použít k zobrazením a zobrazením, včetně [částečných zobrazení](xref:mvc/views/partial) a [rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="04b7a-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="04b7a-236">Následuje příklad, který nastavuje hodnoty pro `ViewData` pozdrav a adresu pomocí v akci:</span><span class="sxs-lookup"><span data-stu-id="04b7a-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="04b7a-237">Práce s daty v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="04b7a-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="04b7a-238">**Atribut ViewData**</span><span class="sxs-lookup"><span data-stu-id="04b7a-238">**ViewData attribute**</span></span>

<span data-ttu-id="04b7a-239">Dalším přístupem, který používá [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) je [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="04b7a-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="04b7a-240">Vlastnosti na řadičích nebo modelech Razor Page označených `[ViewData]` atributem mají své hodnoty uložené a načtené ze slovníku.</span><span class="sxs-lookup"><span data-stu-id="04b7a-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="04b7a-241">V následujícím příkladu obsahuje domácí `Title` kontrolor `[ViewData]`vlastnost označenou .</span><span class="sxs-lookup"><span data-stu-id="04b7a-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="04b7a-242">Metoda `About` nastaví název zobrazení Informace:</span><span class="sxs-lookup"><span data-stu-id="04b7a-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="04b7a-243">V rozložení se název čte ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="04b7a-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="04b7a-244">**Zobrazit tašku**</span><span class="sxs-lookup"><span data-stu-id="04b7a-244">**ViewBag**</span></span>

<span data-ttu-id="04b7a-245">`ViewBag`*není k dispozici v nástroji Razor Pages.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="04b7a-246">`ViewBag`je objekt [DynamicViewData,](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) který poskytuje dynamický `ViewData`přístup k objektům uloženým v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="04b7a-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="04b7a-247">`ViewBag`může být pohodlnější pracovat s, protože nevyžaduje odlévání.</span><span class="sxs-lookup"><span data-stu-id="04b7a-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="04b7a-248">Následující příklad ukazuje, `ViewBag` jak používat se `ViewData` stejným výsledkem jako použití výše:</span><span class="sxs-lookup"><span data-stu-id="04b7a-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="04b7a-249">**Současné použití viewdata a viewbagu**</span><span class="sxs-lookup"><span data-stu-id="04b7a-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="04b7a-250">`ViewBag`*není k dispozici v nástroji Razor Pages.*</span><span class="sxs-lookup"><span data-stu-id="04b7a-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="04b7a-251">Vzhledem `ViewBag` k tomu, `ViewData` `ViewData` a odkazovat `ViewData` na `ViewBag` stejné základní kolekce, můžete použít i a kombinovat mezi nimi při čtení a zápisu hodnoty.</span><span class="sxs-lookup"><span data-stu-id="04b7a-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="04b7a-252">Nastavte název `ViewBag` pomocí a `ViewData` popis pomocí v horní části zobrazení *About.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="04b7a-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="04b7a-253">Přečtěte si vlastnosti, `ViewData` `ViewBag`ale zvrátit použití a .</span><span class="sxs-lookup"><span data-stu-id="04b7a-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="04b7a-254">V souboru *_Layout.cshtml* získejte `ViewData` název pomocí `ViewBag`a získejte popis pomocí :</span><span class="sxs-lookup"><span data-stu-id="04b7a-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="04b7a-255">Nezapomeňte, že struny nevyžadují `ViewData`obsazení pro .</span><span class="sxs-lookup"><span data-stu-id="04b7a-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="04b7a-256">Můžete použít `@ViewData["Title"]` bez odlévání.</span><span class="sxs-lookup"><span data-stu-id="04b7a-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="04b7a-257">Použití `ViewData` obou `ViewBag` a současně funguje, stejně jako míchání a odpovídající čtení a zápis vlastností.</span><span class="sxs-lookup"><span data-stu-id="04b7a-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="04b7a-258">Vykreslují se následující značky:</span><span class="sxs-lookup"><span data-stu-id="04b7a-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="04b7a-259">**Souhrn rozdílů mezi ViewData a ViewBag**</span><span class="sxs-lookup"><span data-stu-id="04b7a-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="04b7a-260">`ViewBag`není k dispozici na stránkách Razor.</span><span class="sxs-lookup"><span data-stu-id="04b7a-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="04b7a-261">Odvozuje z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), takže má vlastnosti slovníku, `Remove`které `Clear`mohou být užitečné, například `ContainsKey`, `Add`, a .</span><span class="sxs-lookup"><span data-stu-id="04b7a-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="04b7a-262">Klávesy ve slovníku jsou řetězce, takže mezery jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="04b7a-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="04b7a-263">Příklad: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="04b7a-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="04b7a-264">V zobrazení, `string` které má být možné `ViewData`použít, musí být přetypován jakýkoli jiný typ než a .</span><span class="sxs-lookup"><span data-stu-id="04b7a-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="04b7a-265">Odvozuje se od [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), takže umožňuje vytváření dynamických vlastností pomocí notace tečky (`@ViewBag.SomeKey = <value or object>`) a není vyžadovánžádný přetypování.</span><span class="sxs-lookup"><span data-stu-id="04b7a-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="04b7a-266">Syntaxe `ViewBag` umožňuje rychlejší přidání do řadičů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="04b7a-267">Jednodušší kontrola nulových hodnot.</span><span class="sxs-lookup"><span data-stu-id="04b7a-267">Simpler to check for null values.</span></span> <span data-ttu-id="04b7a-268">Příklad: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="04b7a-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="04b7a-269">**Kdy použít ViewData nebo ViewBag**</span><span class="sxs-lookup"><span data-stu-id="04b7a-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="04b7a-270">Oba `ViewData` `ViewBag` a jsou stejně platné přístupy pro předávání malých množství dat mezi správci a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="04b7a-271">Volba, kterou z nich použít, je založena na preferencích.</span><span class="sxs-lookup"><span data-stu-id="04b7a-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="04b7a-272">Můžete kombinovat `ViewData` a `ViewBag` objekty, ale kód je snazší číst a udržovat s jedním přístupem používá konzistentně.</span><span class="sxs-lookup"><span data-stu-id="04b7a-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="04b7a-273">Oba přístupy jsou dynamicky vyřešeny za běhu a proto náchylné k vyvolání chyb za běhu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="04b7a-274">Některé vývojové týmy se jim vyhýbají.</span><span class="sxs-lookup"><span data-stu-id="04b7a-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="04b7a-275">Dynamické pohledy</span><span class="sxs-lookup"><span data-stu-id="04b7a-275">Dynamic views</span></span>

<span data-ttu-id="04b7a-276">Pohledy, které nedeklarují typ modelu pomocí, `@model` ale které `return View(Address);`mají instanci modelu, která jim byla předána (například) mohou dynamicky odkazovat na vlastnosti instance:</span><span class="sxs-lookup"><span data-stu-id="04b7a-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="04b7a-277">Tato funkce nabízí flexibilitu, ale nenabízí ochranu kompilace nebo technologie IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="04b7a-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="04b7a-278">Pokud vlastnost neexistuje, generování webových stránek se nezdaří za běhu.</span><span class="sxs-lookup"><span data-stu-id="04b7a-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="04b7a-279">Další funkce zobrazení</span><span class="sxs-lookup"><span data-stu-id="04b7a-279">More view features</span></span>

<span data-ttu-id="04b7a-280">[Pomocníci tagů](xref:mvc/views/tag-helpers/intro) usnadňují přidání chování na straně serveru ke stávajícím značkám HTML.</span><span class="sxs-lookup"><span data-stu-id="04b7a-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="04b7a-281">Použití pomocníků značek se vyhne nutnosti psát vlastní kód nebo pomocníky ve vašich zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="04b7a-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="04b7a-282">Pomocné složky tagů se aplikují jako atributy prvků HTML a editory, kteří je nemohou zpracovat, je ignorují.</span><span class="sxs-lookup"><span data-stu-id="04b7a-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="04b7a-283">To umožňuje upravovat a vykreslovat značky zobrazení v různých nástrojích.</span><span class="sxs-lookup"><span data-stu-id="04b7a-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="04b7a-284">Generování vlastních značek HTML lze dosáhnout pomocí mnoha vestavěných pomocníků HTML.</span><span class="sxs-lookup"><span data-stu-id="04b7a-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="04b7a-285">Složitější logiku uživatelského rozhraní lze zpracovat [pomocí komponent view components](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="04b7a-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="04b7a-286">Komponenty zobrazení poskytují stejné SoC, které nabízejí řadiče a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="04b7a-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="04b7a-287">Mohou eliminovat potřebu akcí a zobrazení, které se zabývají daty používanými běžnými prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04b7a-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="04b7a-288">Stejně jako mnoho jiných aspektů ASP.NET Core, zobrazení podporují [vkládání závislostí](xref:fundamentals/dependency-injection), což umožňuje služby, které mají být [vloženy do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="04b7a-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
