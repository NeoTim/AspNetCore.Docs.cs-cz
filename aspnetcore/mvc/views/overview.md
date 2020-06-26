---
title: Zobrazení ve ASP.NET Core MVC
author: ardalis
description: Přečtěte si, jak zobrazení zpracovávají data prezentace aplikace a interakce uživatele v ASP.NET Core MVC.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/overview
ms.openlocfilehash: b646908c070a51aac2e376c2acaa8e0e091790b0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400463"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="a02f4-103">Zobrazení ve ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a02f4-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="a02f4-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a02f4-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a02f4-105">Tento dokument vysvětluje zobrazení používaná v aplikacích ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a02f4-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="a02f4-106">Informace o Razor stránkách naleznete v tématu [Úvod do Razor stránek](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="a02f4-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="a02f4-107">Ve vzoru MVC (Model-View-Controller) zpracovává *zobrazení* datovou prezentaci aplikace a interakci s uživatelem.</span><span class="sxs-lookup"><span data-stu-id="a02f4-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="a02f4-108">Zobrazení je šablona HTML s vloženým [ Razor kódem](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="a02f4-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> Razor<span data-ttu-id="a02f4-109">označení je kód, který komunikuje s označením HTML a vytvoří webovou stránku, která je odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="a02f4-109"> markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="a02f4-110">V ASP.NET Core MVC jsou zobrazení soubory *. cshtml* , které používají [programovací jazyk C#](/dotnet/csharp/) v Razor kódu.</span><span class="sxs-lookup"><span data-stu-id="a02f4-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="a02f4-111">Soubory zobrazení jsou obvykle seskupeny do složek pojmenovaných pro každý z [řadičů](xref:mvc/controllers/actions)aplikace.</span><span class="sxs-lookup"><span data-stu-id="a02f4-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="a02f4-112">Složky se ukládají do složky *zobrazení* v kořenovém adresáři aplikace:</span><span class="sxs-lookup"><span data-stu-id="a02f4-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Složka zobrazení v Průzkumník řešení sady Visual Studio je otevřená s otevřenou domovskou složkou, která zobrazuje informace o souborech. cshtml, Contact. cshtml a index. cshtml.](overview/_static/views_solution_explorer.png)

<span data-ttu-id="a02f4-114">*Domovský* kontroler je reprezentován *domovskou* složkou ve složce *zobrazení* .</span><span class="sxs-lookup"><span data-stu-id="a02f4-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="a02f4-115">*Domovská* složka obsahuje zobrazení webových stránek *About*, *Contact*a *index* (Domovská stránka).</span><span class="sxs-lookup"><span data-stu-id="a02f4-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="a02f4-116">Když si uživatel vyžádá jednu z těchto tří webových stránek, akce kontroleru v rámci *domovského* kontroleru určují, které ze tří zobrazení se použije k sestavení a vrácení webové stránky uživateli.</span><span class="sxs-lookup"><span data-stu-id="a02f4-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="a02f4-117">Pomocí [rozložení](xref:mvc/views/layout) můžete poskytovat konzistentní oddíly webové stránky a snižovat opakování kódu.</span><span class="sxs-lookup"><span data-stu-id="a02f4-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="a02f4-118">Rozložení často obsahují prvky záhlaví, navigace a nabídky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="a02f4-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="a02f4-119">Záhlaví a zápatí obvykle obsahuje často používaný kód pro mnoho prvků metadat a odkazy na prostředky skriptu a stylu.</span><span class="sxs-lookup"><span data-stu-id="a02f4-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="a02f4-120">Rozložení umožňují vyhnout se tomuto standardnímu označení ve vašich zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="a02f4-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="a02f4-121">[Částečná zobrazení](xref:mvc/views/partial) omezují duplicity kódu tím, že spravují opakovaně použitelné části zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="a02f4-122">Například částečné zobrazení je užitečné v případě, že autorský biografing na webu blogu, který se zobrazuje v několika zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="a02f4-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="a02f4-123">Biografická biografie je běžné zobrazení obsahu a nevyžaduje spuštění kódu, aby bylo možné vytvořit obsah pro webovou stránku.</span><span class="sxs-lookup"><span data-stu-id="a02f4-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="a02f4-124">Vytváření biografických obsahu je k dispozici pro zobrazení podle samotného modelu, takže použití částečného zobrazení pro tento typ obsahu je ideální.</span><span class="sxs-lookup"><span data-stu-id="a02f4-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="a02f4-125">[Zobrazení komponent](xref:mvc/views/view-components) je podobné jako u částečných zobrazení v tom, že umožňují snížit opakující se kód, ale jsou vhodné pro zobrazení obsahu, který vyžaduje spuštění kódu na serveru, aby bylo možné webovou stránku vykreslit.</span><span class="sxs-lookup"><span data-stu-id="a02f4-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="a02f4-126">Zobrazit součásti jsou užitečné, když vykreslený obsah vyžaduje interakci s databází, například pro nákupní košík webu.</span><span class="sxs-lookup"><span data-stu-id="a02f4-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="a02f4-127">Zobrazit součásti nejsou omezeny na vazbu modelu, aby bylo možné vydávat výstup webové stránky.</span><span class="sxs-lookup"><span data-stu-id="a02f4-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="a02f4-128">Výhody používání zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-128">Benefits of using views</span></span>

<span data-ttu-id="a02f4-129">Zobrazení usnadňují vytvoření [oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) v rámci aplikace MVC oddělením značek uživatelského rozhraní od jiných částí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a02f4-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="a02f4-130">Následující návrh SoC vám nabízí modulární aplikaci, která poskytuje několik výhod:</span><span class="sxs-lookup"><span data-stu-id="a02f4-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="a02f4-131">Aplikaci je možné spravovat snadněji, protože je lépe organizovaná.</span><span class="sxs-lookup"><span data-stu-id="a02f4-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="a02f4-132">Zobrazení jsou obecně seskupena podle funkcí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a02f4-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="a02f4-133">To usnadňuje hledání souvisejících zobrazení při práci na funkci.</span><span class="sxs-lookup"><span data-stu-id="a02f4-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="a02f4-134">Části aplikace se volně odpojí.</span><span class="sxs-lookup"><span data-stu-id="a02f4-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="a02f4-135">Zobrazení aplikace můžete sestavovat a aktualizovat odděleně od obchodní logiky a komponent pro přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="a02f4-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="a02f4-136">Můžete upravit zobrazení aplikace, aniž by bylo nutné aktualizovat ostatní části aplikace.</span><span class="sxs-lookup"><span data-stu-id="a02f4-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="a02f4-137">Je snazší otestovat části aplikace uživatelského rozhraní, protože zobrazení jsou samostatné jednotky.</span><span class="sxs-lookup"><span data-stu-id="a02f4-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="a02f4-138">Kvůli lepší organizaci je méně pravděpodobný, že nechtěně zopakujete části uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a02f4-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="a02f4-139">Vytvoření zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-139">Creating a view</span></span>

<span data-ttu-id="a02f4-140">Zobrazení, která jsou specifická pro kontroler, se vytvářejí ve složce *views/[Controller]* .</span><span class="sxs-lookup"><span data-stu-id="a02f4-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="a02f4-141">Zobrazení, která jsou sdílená mezi řadiči, se nacházejí v *zobrazeních nebo sdílených* složkách.</span><span class="sxs-lookup"><span data-stu-id="a02f4-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="a02f4-142">Chcete-li vytvořit zobrazení, přidejte nový soubor a pojmenujte ho stejným názvem jako jeho přidružená akce kontroleru s příponou souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a02f4-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="a02f4-143">Chcete-li vytvořit zobrazení, které odpovídá akci *o* akci v rámci *domovského* kontroleru, vytvořte soubor *About. cshtml* v *zobrazeních/domovské* složce:</span><span class="sxs-lookup"><span data-stu-id="a02f4-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="a02f4-144">*Razor* označení začíná `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="a02f4-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="a02f4-145">Příkazy jazyka C# spustíte tak, že umístíte kód C# do [ Razor bloků kódu](xref:mvc/views/razor#razor-code-blocks) nastavených na složené závorky ( `{ ... }` ).</span><span class="sxs-lookup"><span data-stu-id="a02f4-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="a02f4-146">Podívejte se například na téma přiřazení "About", které se `ViewData["Title"]` zobrazí výše.</span><span class="sxs-lookup"><span data-stu-id="a02f4-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="a02f4-147">Hodnoty v jazyce HTML můžete zobrazit pouhým odkazem na hodnotu `@` symbolem.</span><span class="sxs-lookup"><span data-stu-id="a02f4-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="a02f4-148">Podívejte se na obsah `<h2>` `<h3>` výše uvedených prvků a.</span><span class="sxs-lookup"><span data-stu-id="a02f4-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="a02f4-149">Výše uvedený obsah zobrazení je pouze součástí celé webové stránky, která je uživateli vykreslena.</span><span class="sxs-lookup"><span data-stu-id="a02f4-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="a02f4-150">Zbývající rozložení stránky a další běžné aspekty zobrazení jsou uvedeny v jiných souborech zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="a02f4-151">Další informace najdete v [tématu věnovaném rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a02f4-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="a02f4-152">Jak řadiče určují zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-152">How controllers specify views</span></span>

<span data-ttu-id="a02f4-153">Zobrazení jsou obvykle vrácena z akcí jako [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), což je typ [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span><span class="sxs-lookup"><span data-stu-id="a02f4-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="a02f4-154">Vaše metoda akce může vytvořit a vrátit `ViewResult` přímo, ale to se obvykle neprovádí.</span><span class="sxs-lookup"><span data-stu-id="a02f4-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="a02f4-155">Vzhledem k tomu, že většina řadičů dědí z [kontroleru](/dotnet/api/microsoft.aspnetcore.mvc.controller), jednoduše použijete `View` pomocnou metodu, která vrátí `ViewResult` :</span><span class="sxs-lookup"><span data-stu-id="a02f4-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="a02f4-156">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="a02f4-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="a02f4-157">Když se tato akce vrátí, zobrazení *About. cshtml* zobrazené v poslední části se vykreslí jako následující webová stránka:</span><span class="sxs-lookup"><span data-stu-id="a02f4-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![O stránce vygenerované v prohlížeči Edge](overview/_static/about-page.png)

<span data-ttu-id="a02f4-159">`View`Pomocná metoda má několik přetížení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="a02f4-160">Volitelně můžete zadat:</span><span class="sxs-lookup"><span data-stu-id="a02f4-160">You can optionally specify:</span></span>

* <span data-ttu-id="a02f4-161">Explicitní zobrazení, které se má vrátit:</span><span class="sxs-lookup"><span data-stu-id="a02f4-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="a02f4-162">[Model](xref:mvc/models/model-binding) , který se má předat zobrazení:</span><span class="sxs-lookup"><span data-stu-id="a02f4-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="a02f4-163">Jak zobrazení, tak i model:</span><span class="sxs-lookup"><span data-stu-id="a02f4-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="a02f4-164">Zjišťování zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-164">View discovery</span></span>

<span data-ttu-id="a02f4-165">Když akce vrátí zobrazení, dojde k procesu s názvem *zjišťování zobrazení* .</span><span class="sxs-lookup"><span data-stu-id="a02f4-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="a02f4-166">Tento proces určuje, který soubor zobrazení se použije v závislosti na názvu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="a02f4-167">Výchozí chování `View` metody ( `return View();` ) je vrátit zobrazení se stejným názvem jako metoda akce, ze které je volána.</span><span class="sxs-lookup"><span data-stu-id="a02f4-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="a02f4-168">Například *About* `ActionResult` název metody řadiče se používá k vyhledání souboru zobrazení s názvem *About. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a02f4-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="a02f4-169">Za prvé, modul runtime pro zobrazení vyhledá složku views */[Controller]* .</span><span class="sxs-lookup"><span data-stu-id="a02f4-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="a02f4-170">Pokud tam nenajde žádné zobrazení, vyhledá pro zobrazení *sdílenou* složku.</span><span class="sxs-lookup"><span data-stu-id="a02f4-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="a02f4-171">Nezáleží na tom, zda implicitně vrátíte `ViewResult` `return View();` nebo explicitně předáte název zobrazení do `View` metody pomocí `return View("<ViewName>");` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="a02f4-172">V obou případech zobrazení vyhledávání vyhledá v tomto pořadí soubor zobrazení s porovnáním:</span><span class="sxs-lookup"><span data-stu-id="a02f4-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="a02f4-173">*Zobrazení/ \[ kontrolér]/ \[ viewName]. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a02f4-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="a02f4-174">*Zobrazení/Shared/ \[ viewName]. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a02f4-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="a02f4-175">Místo názvu zobrazení je možné zadat cestu k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="a02f4-176">Pokud použijete absolutní cestu začínající v kořenu aplikace (volitelně začíná znakem "/" nebo "~/"), musí být zadáno rozšíření *. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a02f4-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="a02f4-177">Můžete také použít relativní cestu k určení zobrazení v různých adresářích bez přípony *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a02f4-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="a02f4-178">V `HomeController` nástroji můžete vrátit zobrazení *indexu* pro *správu* zobrazení s relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="a02f4-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="a02f4-179">Podobně můžete určit aktuální adresář specifický pro řadič s předponou "./":</span><span class="sxs-lookup"><span data-stu-id="a02f4-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="a02f4-180">[Částečně zobrazení](xref:mvc/views/partial) a [součásti zobrazení](xref:mvc/views/view-components) používají podobné (ale ne stejné) mechanismy zjišťování.</span><span class="sxs-lookup"><span data-stu-id="a02f4-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="a02f4-181">Můžete přizpůsobit výchozí konvenci pro způsob, jakým se v aplikaci nacházejí zobrazení pomocí vlastního [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span><span class="sxs-lookup"><span data-stu-id="a02f4-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="a02f4-182">Zjišťování zobrazení spoléhá na hledání souborů zobrazení podle názvu souboru.</span><span class="sxs-lookup"><span data-stu-id="a02f4-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="a02f4-183">Pokud základní systém souborů rozlišuje velká a malá písmena, je pravděpodobné, že názvy zobrazení budou rozlišovat velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="a02f4-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="a02f4-184">V případě kompatibility mezi operačními systémy porovnává malá a velká písmena mezi názvy kontrolérů a akcí a přidružených složek zobrazení a názvů souborů.</span><span class="sxs-lookup"><span data-stu-id="a02f4-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="a02f4-185">Pokud dojde k chybě, že soubor zobrazení nebyl nalezen při práci se systémem souborů s rozlišováním velkých a malých písmen, potvrďte, že velikost písmen mezi požadovaným souborem zobrazení a aktuálním názvem souboru zobrazení se neshoduje.</span><span class="sxs-lookup"><span data-stu-id="a02f4-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="a02f4-186">Dodržujte osvědčené postupy uspořádání struktury souborů pro vaše zobrazení tak, aby odrážely vztahy mezi řadiči, akcemi a zobrazeními pro udržovatelnost a přehlednost.</span><span class="sxs-lookup"><span data-stu-id="a02f4-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="a02f4-187">Předávání dat do zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-187">Passing data to views</span></span>

<span data-ttu-id="a02f4-188">Předávání dat do zobrazení pomocí několika přístupů:</span><span class="sxs-lookup"><span data-stu-id="a02f4-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="a02f4-189">Data silného typu: ViewModel</span><span class="sxs-lookup"><span data-stu-id="a02f4-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="a02f4-190">Slabě zadaná data</span><span class="sxs-lookup"><span data-stu-id="a02f4-190">Weakly typed data</span></span>
  * <span data-ttu-id="a02f4-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="a02f4-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="a02f4-192">Data silného typu (ViewModel)</span><span class="sxs-lookup"><span data-stu-id="a02f4-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="a02f4-193">Nejrobustním přístupem je určit typ [modelu](xref:mvc/models/model-binding) v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="a02f4-194">Tento model se běžně označuje jako *ViewModel*.</span><span class="sxs-lookup"><span data-stu-id="a02f4-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="a02f4-195">Z akce předáte instanci typu ViewModel do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="a02f4-196">Použití ViewModel k předávání dat zobrazení umožňuje zobrazení výhod kontroly *silného* typu.</span><span class="sxs-lookup"><span data-stu-id="a02f4-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="a02f4-197">*Silné psaní* (nebo *silného typu*) znamená, že každá proměnná a konstanta má explicitně definovaný typ (například `string` , `int` nebo `DateTime` ).</span><span class="sxs-lookup"><span data-stu-id="a02f4-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="a02f4-198">Platnost typů použitých v zobrazení je kontrolována v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="a02f4-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="a02f4-199">[Visual Studio](https://visualstudio.microsoft.com) a [Visual Studio Code](https://code.visualstudio.com/) seznam členů třídy silně typovaného typu pomocí funkce s názvem [IntelliSense](/visualstudio/ide/using-intellisense).</span><span class="sxs-lookup"><span data-stu-id="a02f4-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="a02f4-200">Pokud chcete zobrazit vlastnosti ViewModel, zadejte název proměnné pro ViewModel následovaný tečkou ( `.` ).</span><span class="sxs-lookup"><span data-stu-id="a02f4-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="a02f4-201">To vám pomůže psát kód rychleji s menším množstvím chyb.</span><span class="sxs-lookup"><span data-stu-id="a02f4-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="a02f4-202">Určete model pomocí `@model` direktivy.</span><span class="sxs-lookup"><span data-stu-id="a02f4-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="a02f4-203">Použijte model s `@Model` :</span><span class="sxs-lookup"><span data-stu-id="a02f4-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="a02f4-204">Aby se tento model poskytl zobrazení, kontroler ho předává jako parametr:</span><span class="sxs-lookup"><span data-stu-id="a02f4-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

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

<span data-ttu-id="a02f4-205">Pro typy modelů, které lze poskytnout zobrazení, neexistují žádná omezení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="a02f4-206">Doporučujeme použít prostý starý objekt CLR (POCO) ViewModels s malým nebo žádným chováním (metody) definované.</span><span class="sxs-lookup"><span data-stu-id="a02f4-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="a02f4-207">Třídy ViewModel jsou obvykle uloženy ve složce *modely* nebo v samostatné složce *ViewModels* v kořenovém adresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="a02f4-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="a02f4-208">*Adresa* ViewModel použitá v předchozím příkladu je POCO ViewModel uložená v souboru s názvem *Address.cs*:</span><span class="sxs-lookup"><span data-stu-id="a02f4-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

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

<span data-ttu-id="a02f4-209">Nic nebrání použití stejných tříd pro ViewModel typy a typy obchodních modelů.</span><span class="sxs-lookup"><span data-stu-id="a02f4-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="a02f4-210">Použití samostatných modelů ale umožňuje, aby se vaše zobrazení měnila nezávisle na obchodních logicech a částech aplikace pro přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="a02f4-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="a02f4-211">Oddělení modelů a ViewModels také nabízí výhody zabezpečení, když modely používají [vazby modelů](xref:mvc/models/model-binding) a [ověřování](xref:mvc/models/validation) dat odesílaných do aplikace uživatelem.</span><span class="sxs-lookup"><span data-stu-id="a02f4-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="a02f4-212">Slabě zadaná data (ViewData, atribut ViewData a ViewBag)</span><span class="sxs-lookup"><span data-stu-id="a02f4-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="a02f4-213">`ViewBag`*není k dispozici v Razor Stránky.*</span><span class="sxs-lookup"><span data-stu-id="a02f4-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="a02f4-214">Kromě zobrazení se silným typem mají zobrazení zobrazení přístup k *slabě typované* (také nazývané *volně typované*) kolekci dat.</span><span class="sxs-lookup"><span data-stu-id="a02f4-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="a02f4-215">Na rozdíl od silných typů, *slabých* typů (nebo *volných typů*) znamená, že explicitně nedeklarujete typ dat, která používáte.</span><span class="sxs-lookup"><span data-stu-id="a02f4-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="a02f4-216">Pro přenos malých objemů dat do a z řadičů a zobrazení můžete použít kolekci slabého typu dat.</span><span class="sxs-lookup"><span data-stu-id="a02f4-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="a02f4-217">Předávání dat mezi...</span><span class="sxs-lookup"><span data-stu-id="a02f4-217">Passing data between a ...</span></span>                        | <span data-ttu-id="a02f4-218">Příklad</span><span class="sxs-lookup"><span data-stu-id="a02f4-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="a02f4-219">Kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-219">Controller and a view</span></span>                             | <span data-ttu-id="a02f4-220">Naplnění rozevíracího seznamu daty.</span><span class="sxs-lookup"><span data-stu-id="a02f4-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="a02f4-221">Zobrazení a [zobrazení rozložení](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="a02f4-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="a02f4-222">Nastavení **\<title>** obsahu prvku v zobrazení rozložení ze souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="a02f4-223">[Částečné zobrazení](xref:mvc/views/partial) a zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="a02f4-224">Pomůcka, která zobrazuje data na základě webové stránky požadované uživatelem.</span><span class="sxs-lookup"><span data-stu-id="a02f4-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="a02f4-225">Na tuto kolekci lze odkazovat buď pomocí `ViewData` vlastností nebo `ViewBag` v řadičích a zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="a02f4-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="a02f4-226">`ViewData`Vlastnost je slovníkem slabě typových objektů.</span><span class="sxs-lookup"><span data-stu-id="a02f4-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="a02f4-227">`ViewBag`Vlastnost je Obálka kolem `ViewData` , která poskytuje dynamické vlastnosti pro podkladovou `ViewData` kolekci.</span><span class="sxs-lookup"><span data-stu-id="a02f4-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="a02f4-228">Poznámka: Vyhledání klíčů rozlišuje velká a malá písmena `ViewData` `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="a02f4-229">`ViewData`a `ViewBag` jsou dynamicky vyřešeny za běhu.</span><span class="sxs-lookup"><span data-stu-id="a02f4-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="a02f4-230">Vzhledem k tomu, že nenabízejí kontrolu typu při kompilaci, obě jsou obecně větší náchylnější k chybám než použití ViewModel.</span><span class="sxs-lookup"><span data-stu-id="a02f4-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="a02f4-231">Z tohoto důvodu někteří vývojáři dávají přednost minimálnímu nebo nikdy nepoužívání `ViewData` a `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="a02f4-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="a02f4-232">**ViewData**</span></span>

<span data-ttu-id="a02f4-233">`ViewData`je objekt [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) , ke kterému se přistupoval prostřednictvím `string` klíčů.</span><span class="sxs-lookup"><span data-stu-id="a02f4-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="a02f4-234">Řetězcová data lze uložit a použít přímo bez nutnosti přetypování, ale `ViewData` při jejich extrakci je nutné přetypovat jiné hodnoty objektů na konkrétní typy.</span><span class="sxs-lookup"><span data-stu-id="a02f4-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="a02f4-235">Můžete použít `ViewData` k předávání dat z řadičů do zobrazení a v rámci zobrazení, včetně [částečných zobrazení](xref:mvc/views/partial) a [rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a02f4-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="a02f4-236">Následuje příklad, který nastavuje hodnoty pro pozdrav a adresu pomocí `ViewData` akce:</span><span class="sxs-lookup"><span data-stu-id="a02f4-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

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

<span data-ttu-id="a02f4-237">Práce s daty v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="a02f4-237">Work with the data in a view:</span></span>

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

<span data-ttu-id="a02f4-238">**ViewData – atribut**</span><span class="sxs-lookup"><span data-stu-id="a02f4-238">**ViewData attribute**</span></span>

<span data-ttu-id="a02f4-239">Dalším přístupem, který používá [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) , je [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="a02f4-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="a02f4-240">Vlastnosti v řadičích nebo Razor modelech stránek označených `[ViewData]` atributem mají jejich hodnoty uložené a načtené ze slovníku.</span><span class="sxs-lookup"><span data-stu-id="a02f4-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="a02f4-241">V následujícím příkladu obsahuje domovský kontroler `Title` vlastnost s označením `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="a02f4-242">`About`Metoda nastaví název zobrazení informace o:</span><span class="sxs-lookup"><span data-stu-id="a02f4-242">The `About` method sets the title for the About view:</span></span>

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

<span data-ttu-id="a02f4-243">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="a02f4-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="a02f4-244">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="a02f4-244">**ViewBag**</span></span>

<span data-ttu-id="a02f4-245">`ViewBag`*není k dispozici v Razor Stránky.*</span><span class="sxs-lookup"><span data-stu-id="a02f4-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="a02f4-246">`ViewBag`je objekt [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) , který poskytuje dynamický přístup k objektům uloženým v `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="a02f4-247">`ViewBag`může být pohodlnější pro práci s, protože nevyžaduje přetypování.</span><span class="sxs-lookup"><span data-stu-id="a02f4-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="a02f4-248">Následující příklad ukazuje, jak použít `ViewBag` se stejným výsledkem jako v použití `ViewData` výše:</span><span class="sxs-lookup"><span data-stu-id="a02f4-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

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

<span data-ttu-id="a02f4-249">**Použití ViewData a ViewBag současně**</span><span class="sxs-lookup"><span data-stu-id="a02f4-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="a02f4-250">`ViewBag`*není k dispozici v Razor Stránky.*</span><span class="sxs-lookup"><span data-stu-id="a02f4-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="a02f4-251">Vzhledem `ViewData` k tomu, že a `ViewBag` odkazují na stejnou základní `ViewData` kolekci, můžete `ViewData` `ViewBag` při čtení a zápisu hodnot použít jak a, tak i kombinaci a porovnávání.</span><span class="sxs-lookup"><span data-stu-id="a02f4-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="a02f4-252">Pomocí nadpisu `ViewBag` a popisu použijte `ViewData` v horní části o zobrazení o souboru *. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a02f4-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="a02f4-253">Přečtěte si vlastnosti, ale zpět použijte `ViewData` a `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="a02f4-254">V souboru *_Layout. cshtml* Získejte název pomocí `ViewData` a získejte popis pomocí `ViewBag` :</span><span class="sxs-lookup"><span data-stu-id="a02f4-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="a02f4-255">Pamatujte, že řetězce nevyžadují přetypování pro `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="a02f4-256">Můžete použít `@ViewData["Title"]` bez přetypování.</span><span class="sxs-lookup"><span data-stu-id="a02f4-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="a02f4-257">Použití obou `ViewData` i `ViewBag` ve stejnou dobu funguje, stejně jako kombinace a spárování se čtením a zápisem vlastností.</span><span class="sxs-lookup"><span data-stu-id="a02f4-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="a02f4-258">Následující kód je vykreslen:</span><span class="sxs-lookup"><span data-stu-id="a02f4-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="a02f4-259">**Shrnutí rozdílů mezi ViewData a ViewBag**</span><span class="sxs-lookup"><span data-stu-id="a02f4-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="a02f4-260">`ViewBag`není na stránkách k dispozici Razor .</span><span class="sxs-lookup"><span data-stu-id="a02f4-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="a02f4-261">Je odvozen z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), takže má vlastnosti slovníku, které mohou být užitečné, například `ContainsKey` , `Add` , `Remove` a `Clear` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="a02f4-262">Klíče ve slovníku jsou řetězce, takže je povolen prázdný znak.</span><span class="sxs-lookup"><span data-stu-id="a02f4-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="a02f4-263">Příklad: `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="a02f4-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="a02f4-264">Libovolný typ jiný než `string` musí být přetypování v zobrazení, které se má použít `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="a02f4-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="a02f4-265">Je odvozen z [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), takže umožňuje vytváření dynamických vlastností pomocí tečky Notation ( `@ViewBag.SomeKey = <value or object>` ) a není vyžadováno přetypování.</span><span class="sxs-lookup"><span data-stu-id="a02f4-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="a02f4-266">Syntaxe nástroje `ViewBag` umožňuje rychlejší přidávání do řadičů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="a02f4-267">Jednodušší pro kontrolu hodnot null.</span><span class="sxs-lookup"><span data-stu-id="a02f4-267">Simpler to check for null values.</span></span> <span data-ttu-id="a02f4-268">Příklad: `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="a02f4-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="a02f4-269">**Kdy použít ViewData nebo ViewBag**</span><span class="sxs-lookup"><span data-stu-id="a02f4-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="a02f4-270">Oba `ViewData` i `ViewBag` jsou stejně platné přístupy k předávání malých objemů dat mezi řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="a02f4-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="a02f4-271">Výběr, který má být použit, je založen na předvolbách.</span><span class="sxs-lookup"><span data-stu-id="a02f4-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="a02f4-272">Můžete kombinovat objekty a porovnat `ViewData` je `ViewBag` , ale kód je snazší číst a udržovat s jedním způsobem použitým konzistentně.</span><span class="sxs-lookup"><span data-stu-id="a02f4-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="a02f4-273">Oba přístupy se dynamicky vyřeší za běhu, a proto náchylné k chybám za běhu.</span><span class="sxs-lookup"><span data-stu-id="a02f4-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="a02f4-274">Některé vývojové týmy je zabraňují.</span><span class="sxs-lookup"><span data-stu-id="a02f4-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="a02f4-275">Dynamická zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-275">Dynamic views</span></span>

<span data-ttu-id="a02f4-276">Zobrazení, která nedeklarují typ modelu pomocí `@model` , ale které mají předané instance modelu (například), `return View(Address);` mohou dynamicky odkazovat na vlastnosti instance:</span><span class="sxs-lookup"><span data-stu-id="a02f4-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="a02f4-277">Tato funkce nabízí flexibilitu, ale nenabízí ochranu kompilace ani IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="a02f4-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="a02f4-278">Pokud vlastnost neexistuje, generování webové stránky se za běhu nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="a02f4-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="a02f4-279">Další funkce zobrazení</span><span class="sxs-lookup"><span data-stu-id="a02f4-279">More view features</span></span>

<span data-ttu-id="a02f4-280">[Pomocník značek](xref:mvc/views/tag-helpers/intro) usnadňuje přidávání chování na straně serveru do existujících značek HTML.</span><span class="sxs-lookup"><span data-stu-id="a02f4-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="a02f4-281">Použití pomocníků značek zabraňuje nutnosti psát vlastní kód nebo pomocníky v rámci vašich zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a02f4-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="a02f4-282">Pomocníky značek se používají jako atributy pro prvky HTML a jsou ignorovány editory, které je nemohou zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a02f4-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="a02f4-283">To vám umožní upravovat a vykreslovat značky zobrazení v nejrůznějších nástrojích.</span><span class="sxs-lookup"><span data-stu-id="a02f4-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="a02f4-284">Generování vlastního kódu HTML lze dosáhnout pomocí mnoha vestavěných pomocníků HTML.</span><span class="sxs-lookup"><span data-stu-id="a02f4-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="a02f4-285">Složitější logiku uživatelského rozhraní lze zpracovat pomocí [zobrazení komponent](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="a02f4-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="a02f4-286">Zobrazit součásti poskytují stejné SoC, jaké řadiče a zobrazení nabízí.</span><span class="sxs-lookup"><span data-stu-id="a02f4-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="a02f4-287">Můžou eliminovat nutnost akcí a zobrazení, které se týkají dat používaných společnými prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a02f4-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="a02f4-288">Podobně jako mnoho dalších aspektů ASP.NET Core podporují [vkládání závislostí](xref:fundamentals/dependency-injection), což umožňuje vkládání služeb [do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a02f4-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
