---
title: Migrace z ASP.NET MVC na ASP.NET Core MVC
author: ardalis
description: Naučte se, jak začít migrovat projekt ASP.NET MVC na ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777043"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="7257e-103">Migrace z ASP.NET MVC na ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="7257e-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="7257e-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Skořepa](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="7257e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="7257e-105">Tento článek ukazuje, jak začít s migrací projektu ASP.NET MVC na [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="7257e-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="7257e-106">V tomto procesu se zvýrazní spousta věcí, které se změnily z ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="7257e-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="7257e-107">Migrace z ASP.NET MVC je proces více kroků a tento článek se zabývá počátečním nastavením, základními řadiči a zobrazeními, statickým obsahem a závislostmi na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="7257e-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="7257e-108">Další články zahrnují migraci konfigurace a kódu identity nalezeného v mnoha projektech ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="7257e-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="7257e-109">Čísla verzí v ukázkách nemusí být aktuální.</span><span class="sxs-lookup"><span data-stu-id="7257e-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="7257e-110">Vaše projekty možná budete muset aktualizovat odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="7257e-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="7257e-111">Vytvoření projektu počáteční ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="7257e-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="7257e-112">K předvedení upgradu začneme vytvořením aplikace ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="7257e-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="7257e-113">Vytvořte ho s názvem *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu, který jsme vytvořili v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="7257e-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Dialogové okno Nový projekt sady Visual Studio](mvc/_static/new-project.png)

![Dialogové okno Nová webová aplikace: šablona projektu MVC vybraná v panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="7257e-116">*Volitelné:* Změňte název řešení z *WebApp1* na *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="7257e-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="7257e-117">Visual Studio zobrazí nový název řešení (*Mvc5*), který usnadňuje podávání tohoto projektu z dalšího projektu.</span><span class="sxs-lookup"><span data-stu-id="7257e-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="7257e-118">Vytvoření projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7257e-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="7257e-119">Vytvořte novou *prázdnou* ASP.NET Core webovou aplikaci se stejným názvem jako předchozí projekt (*WebApp1*), aby se obory názvů ve dvou projektech shodovaly.</span><span class="sxs-lookup"><span data-stu-id="7257e-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="7257e-120">Stejný obor názvů usnadňuje kopírování kódu mezi dvěma projekty.</span><span class="sxs-lookup"><span data-stu-id="7257e-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="7257e-121">Tento projekt bude nutné vytvořit v jiném adresáři než předchozí projekt pro použití stejného názvu.</span><span class="sxs-lookup"><span data-stu-id="7257e-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogové okno Nový projekt](mvc/_static/new_core.png)

![Dialog nové webové aplikace v ASP.NET: prázdná šablona projektu vybraná na panelu šablon ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="7257e-124">*Volitelné:* Vytvořte novou aplikaci ASP.NET Core pomocí šablony projektu *webové aplikace* .</span><span class="sxs-lookup"><span data-stu-id="7257e-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="7257e-125">Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivých uživatelských účtů**.</span><span class="sxs-lookup"><span data-stu-id="7257e-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="7257e-126">Přejmenujte tuto aplikaci na *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="7257e-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="7257e-127">Vytvořením tohoto projektu ušetříte čas v převodu.</span><span class="sxs-lookup"><span data-stu-id="7257e-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="7257e-128">Můžete se podívat na kód vygenerovaný šablonou a zobrazit tak konečný výsledek nebo zkopírovat kód do projektu pro převod.</span><span class="sxs-lookup"><span data-stu-id="7257e-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="7257e-129">Je také užitečné, když se zablokuje na krok převodu pro porovnání s projektem generovaným šablonou.</span><span class="sxs-lookup"><span data-stu-id="7257e-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="7257e-130">Konfigurace lokality pro použití MVC</span><span class="sxs-lookup"><span data-stu-id="7257e-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="7257e-131">Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="7257e-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="7257e-132">Tento balíček obsahuje balíčky běžně používané aplikacemi MVC.</span><span class="sxs-lookup"><span data-stu-id="7257e-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="7257e-133">Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7257e-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="7257e-134">Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage) .</span><span class="sxs-lookup"><span data-stu-id="7257e-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="7257e-135">Tento balíček obsahuje balíčky běžně používané balíčky aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="7257e-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="7257e-136">Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7257e-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="7257e-137">Při cílení na rozhraní .NET Core nebo .NET Framework jsou balíčky běžně používané balíčky MVC aplikace uvedeny jednotlivě v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="7257e-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="7257e-138">`Microsoft.AspNetCore.Mvc`je ASP.NET Core architekturu MVC.</span><span class="sxs-lookup"><span data-stu-id="7257e-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="7257e-139">`Microsoft.AspNetCore.StaticFiles`je obslužná rutina statického souboru.</span><span class="sxs-lookup"><span data-stu-id="7257e-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="7257e-140">Modul runtime ASP.NET Core je modulární a musíte explicitně vyjádřit výslovný souhlas s poskytováním statických souborů (viz [statické soubory](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="7257e-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="7257e-141">Otevřete soubor *Startup.cs* a změňte kód tak, aby odpovídal následujícímu:</span><span class="sxs-lookup"><span data-stu-id="7257e-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="7257e-142">Metoda `UseStaticFiles` rozšíření přidá obslužnou rutinu statického souboru.</span><span class="sxs-lookup"><span data-stu-id="7257e-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="7257e-143">Jak už bylo zmíněno dříve, modul runtime ASP.NET je modulární a musíte explicitně vyjádřit výslovný souhlas s poskytováním statických souborů.</span><span class="sxs-lookup"><span data-stu-id="7257e-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="7257e-144">Metoda `UseMvc` rozšíření přidává směrování.</span><span class="sxs-lookup"><span data-stu-id="7257e-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="7257e-145">Další informace najdete v tématu [spuštění](xref:fundamentals/startup) a [Směrování](xref:fundamentals/routing)aplikace.</span><span class="sxs-lookup"><span data-stu-id="7257e-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="7257e-146">Přidat kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="7257e-146">Add a controller and view</span></span>

<span data-ttu-id="7257e-147">V této části přidáte minimální kontroler a zobrazení, které slouží jako zástupné symboly pro kontroler ASP.NET MVC a zobrazení, která migrujete v další části.</span><span class="sxs-lookup"><span data-stu-id="7257e-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="7257e-148">Přidejte složku *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="7257e-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="7257e-149">Do složky *Controllers* přidejte **třídu controller** s názvem *HomeController.cs* .</span><span class="sxs-lookup"><span data-stu-id="7257e-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Dialogové okno Přidat novou položku](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="7257e-151">Přidejte složku *zobrazení* .</span><span class="sxs-lookup"><span data-stu-id="7257e-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="7257e-152">Přidat *zobrazení/domovskou* složku.</span><span class="sxs-lookup"><span data-stu-id="7257e-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="7257e-153">Přidejte \*\* Razor zobrazení\*\* s názvem *index. cshtml* do složky *views/Home* .</span><span class="sxs-lookup"><span data-stu-id="7257e-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Dialogové okno Přidat novou položku](mvc/_static/view.png)

<span data-ttu-id="7257e-155">Struktura projektu je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="7257e-155">The project structure is shown below:</span></span>

![Průzkumník řešení zobrazení souborů a složek WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="7257e-157">Obsah souboru *views/Home/index. cshtml* nahraďte následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="7257e-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="7257e-158">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7257e-158">Run the app.</span></span>

![Webová aplikace otevřená v Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="7257e-160">Další informace najdete v tématu [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview) .</span><span class="sxs-lookup"><span data-stu-id="7257e-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="7257e-161">Teď, když máme minimální pracovní ASP.NET Core projekt, můžeme začít migrovat funkce z projektu MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7257e-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="7257e-162">Musíme přesunout následující:</span><span class="sxs-lookup"><span data-stu-id="7257e-162">We need to move the following:</span></span>

* <span data-ttu-id="7257e-163">obsah na straně klienta (šablony stylů CSS, písma a skripty)</span><span class="sxs-lookup"><span data-stu-id="7257e-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="7257e-164">kontrolery</span><span class="sxs-lookup"><span data-stu-id="7257e-164">controllers</span></span>

* <span data-ttu-id="7257e-165">zobrazení</span><span class="sxs-lookup"><span data-stu-id="7257e-165">views</span></span>

* <span data-ttu-id="7257e-166">modely</span><span class="sxs-lookup"><span data-stu-id="7257e-166">models</span></span>

* <span data-ttu-id="7257e-167">sdružování</span><span class="sxs-lookup"><span data-stu-id="7257e-167">bundling</span></span>

* <span data-ttu-id="7257e-168">filtry</span><span class="sxs-lookup"><span data-stu-id="7257e-168">filters</span></span>

* <span data-ttu-id="7257e-169">Přihlaste se nebo Identity se přihlaste (to se provádí v dalším kurzu).</span><span class="sxs-lookup"><span data-stu-id="7257e-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="7257e-170">Řadiče a zobrazení</span><span class="sxs-lookup"><span data-stu-id="7257e-170">Controllers and views</span></span>

* <span data-ttu-id="7257e-171">Zkopírujte každou z metod z ASP.NET MVC `HomeController` do nové. `HomeController`</span><span class="sxs-lookup"><span data-stu-id="7257e-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="7257e-172">Všimněte si, že ve službě ASP.NET MVC je návratový typ metody kontroleru vestavěné šablony [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC se místo toho vrací `IActionResult` metody akcí.</span><span class="sxs-lookup"><span data-stu-id="7257e-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="7257e-173">`ActionResult`implementuje `IActionResult`, takže není nutné měnit návratový typ vašich metod akcí.</span><span class="sxs-lookup"><span data-stu-id="7257e-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="7257e-174">Zkopírujte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7257e-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="7257e-175">Spusťte aplikaci ASP.NET Core a otestujte všechny metody.</span><span class="sxs-lookup"><span data-stu-id="7257e-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="7257e-176">Zatím jsme nemigrovali soubor rozložení ani styly, takže vykreslená zobrazení obsahují jenom obsah v zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="7257e-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="7257e-177">Nebudete mít odkazy vygenerované souborem rozložení pro `About` zobrazení `Contact` a, takže je budete muset vyvolat z prohlížeče (nahraďte **4492** číslem portu použitým v projektu).</span><span class="sxs-lookup"><span data-stu-id="7257e-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Stránka kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="7257e-179">Všimněte si nedostatku stylů a položek nabídky.</span><span class="sxs-lookup"><span data-stu-id="7257e-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="7257e-180">Opravujeme ho v další části.</span><span class="sxs-lookup"><span data-stu-id="7257e-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="7257e-181">Statický obsah</span><span class="sxs-lookup"><span data-stu-id="7257e-181">Static content</span></span>

<span data-ttu-id="7257e-182">V předchozích verzích ASP.NET MVC byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="7257e-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="7257e-183">V ASP.NET Core je statický obsah hostovaný ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="7257e-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="7257e-184">Budete chtít zkopírovat statický obsah ze staré aplikace ASP.NET MVC do složky *wwwroot* v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7257e-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="7257e-185">V tomto ukázkovém převodu:</span><span class="sxs-lookup"><span data-stu-id="7257e-185">In this sample conversion:</span></span>

* <span data-ttu-id="7257e-186">Zkopírujte soubor *favicon. ico* z původního projektu MVC do složky *wwwroot* v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7257e-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="7257e-187">Starý projekt ASP.NET MVC používá pro svůj styl [bootstrap](https://getbootstrap.com/) a ukládá spouštěcí soubory do složek *obsah* a *skripty* .</span><span class="sxs-lookup"><span data-stu-id="7257e-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="7257e-188">Šablona, která vygenerovala starý projekt ASP.NET MVC, odkazuje na zavedení v souboru rozložení (*views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="7257e-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="7257e-189">Můžete zkopírovat soubory *bootstrap. js* a *bootstrap. css* z projektu ASP.NET MVC do složky *wwwroot* v novém projektu.</span><span class="sxs-lookup"><span data-stu-id="7257e-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="7257e-190">Místo toho přidáme podporu pro Bootstrap (a další knihovny na straně klienta) pomocí sítě CDN v další části.</span><span class="sxs-lookup"><span data-stu-id="7257e-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="7257e-191">Migrovat soubor rozložení</span><span class="sxs-lookup"><span data-stu-id="7257e-191">Migrate the layout file</span></span>

* <span data-ttu-id="7257e-192">Zkopírujte soubor *_ViewStart. cshtml* ze staré složky *zobrazení* projektu ASP.NET MVC do složky *zobrazení* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7257e-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="7257e-193">Soubor *_ViewStart. cshtml* se v ASP.NET Core MVC nezměnil.</span><span class="sxs-lookup"><span data-stu-id="7257e-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="7257e-194">Vytvoří *zobrazení/sdílenou* složku.</span><span class="sxs-lookup"><span data-stu-id="7257e-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="7257e-195">*Volitelné:* Zkopírujte *_ViewImports. cshtml* ze složky *zobrazení* projektu *FullAspNetCore* MVC do složky *zobrazení* projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7257e-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="7257e-196">Odeberte všechny deklarace oboru názvů v souboru *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="7257e-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="7257e-197">Soubor *_ViewImports. cshtml* poskytuje obory názvů pro všechny soubory zobrazení a přináší [pomocníkům značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="7257e-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7257e-198">V novém souboru rozložení se používají pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="7257e-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="7257e-199">Soubor *_ViewImports. cshtml* je pro ASP.NET Core nový.</span><span class="sxs-lookup"><span data-stu-id="7257e-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="7257e-200">Zkopírujte soubor *_Layout. cshtml* ze starých *zobrazení nebo sdílené* složky projektu ASP.NET MVC do *zobrazení nebo* sdílené složky projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7257e-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="7257e-201">Otevřete soubor *_Layout. cshtml* a proveďte následující změny (dokončený kód je zobrazen níže):</span><span class="sxs-lookup"><span data-stu-id="7257e-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="7257e-202">Nahraďte `@Styles.Render("~/Content/css")` `<link>` elementem pro načtení *bootstrap. CSS* (viz níže).</span><span class="sxs-lookup"><span data-stu-id="7257e-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="7257e-203">Odebrat `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="7257e-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="7257e-204">Odkomentujte `@Html.Partial("_LoginPartial")` řádek kolem řádku (Obklopte řádek s `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="7257e-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="7257e-205">Další informace najdete v tématu [migrace ověřování Identity a ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="7257e-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="7257e-206">Nahraďte `@Scripts.Render("~/bundles/jquery")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="7257e-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="7257e-207">Nahraďte `@Scripts.Render("~/bundles/bootstrap")` `<script>` elementem (viz níže).</span><span class="sxs-lookup"><span data-stu-id="7257e-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="7257e-208">Náhradní značky pro spuštění šablony stylů Bootstrap:</span><span class="sxs-lookup"><span data-stu-id="7257e-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="7257e-209">Náhradní označení pro zahrnutí a zavedení JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="7257e-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="7257e-210">Aktualizovaný soubor *_Layout. cshtml* je uveden níže:</span><span class="sxs-lookup"><span data-stu-id="7257e-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="7257e-211">Zobrazí web v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7257e-211">View the site in the browser.</span></span> <span data-ttu-id="7257e-212">Měl by se teď správně načíst s očekávanými styly.</span><span class="sxs-lookup"><span data-stu-id="7257e-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="7257e-213">*Volitelné:* Možná budete chtít zkusit použít nový soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="7257e-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="7257e-214">Pro tento projekt můžete zkopírovat soubor rozložení z projektu *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="7257e-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="7257e-215">Nový soubor rozložení používá [pomocníky značek](xref:mvc/views/tag-helpers/intro) a má další vylepšení.</span><span class="sxs-lookup"><span data-stu-id="7257e-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7257e-216">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="7257e-216">Configure bundling and minification</span></span>

<span data-ttu-id="7257e-217">Informace o tom, jak nakonfigurovat sdružování a minifikace, najdete v článku [sdružování a minifikace](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="7257e-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="7257e-218">Řešení chyb HTTP 500</span><span class="sxs-lookup"><span data-stu-id="7257e-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="7257e-219">Existuje mnoho problémů, které mohou způsobit chybovou zprávu HTTP 500, která neobsahuje žádné informace o zdroji problému.</span><span class="sxs-lookup"><span data-stu-id="7257e-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="7257e-220">Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, zobrazí se chyba HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="7257e-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="7257e-221">Ve výchozím nastavení se v ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7257e-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="7257e-222">Toto je podrobně popsáno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="7257e-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="7257e-223">ASP.NET Core převede neošetřené výjimky ve webové aplikaci na odpovědi na chyby HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="7257e-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="7257e-224">V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru.</span><span class="sxs-lookup"><span data-stu-id="7257e-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="7257e-225">Další informace najdete v tématu **použití stránky s výjimkou vývojáře** v tématu [zpracování chyb](../fundamentals/error-handling.md) .</span><span class="sxs-lookup"><span data-stu-id="7257e-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7257e-226">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="7257e-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
