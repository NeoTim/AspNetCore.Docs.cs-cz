---
title: Migrace z technologie ASP.NET MVC do ASP.NET Core MVC
author: ardalis
description: Zjistěte, jak začít s migrací projektu aplikace ASP.NET MVC do ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: a9e2b41b933ed04a23515564892ed1694a4ac4f8
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614393"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="fc52c-103">Migrace z technologie ASP.NET MVC do ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fc52c-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="fc52c-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="fc52c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="fc52c-105">Tento článek popisuje, jak začít, migrace projektu aplikace ASP.NET MVC [ASP.NET Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="fc52c-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="fc52c-106">V procesu se zvýrazní spousta věcí, které byly změněny z technologie ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="fc52c-107">Migrace z technologie ASP.NET MVC je o více krocích a tento článek se týká počáteční nastavení, základní kontrolerů a zobrazení, statický obsah a závislostí na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="fc52c-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="fc52c-108">Další články zahrnují migraci konfigurací a kódem identit v mnoha projektů ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="fc52c-109">Čísla verzí v ukázkách, nemusí být aktuální.</span><span class="sxs-lookup"><span data-stu-id="fc52c-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="fc52c-110">Budete muset aktualizovat vaše projekty odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="fc52c-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="fc52c-111">Vytvořit úvodní projektu ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="fc52c-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="fc52c-112">Abychom si předvedli upgradu, Začneme tím, že vytvoříte aplikaci ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="fc52c-113">Vytvořte s názvem *WebApp1* tak obor názvů odpovídá projekt ASP.NET Core, vytvoříme v dalším kroku.</span><span class="sxs-lookup"><span data-stu-id="fc52c-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Dialogové okno Visual Studio nový projekt](mvc/_static/new-project.png)

![Dialogové okno nové webové aplikace: Šablona projektu MVC vybrali panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="fc52c-116">*Volitelné:* Změňte název řešení od *WebApp1* k *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="fc52c-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="fc52c-117">Visual Studio zobrazí nový název řešení (*Mvc5*), což usnadňuje zjistit tento projekt z projektu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="fc52c-118">Vytvořit projekt ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc52c-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="fc52c-119">Vytvořte nový *prázdný* webové aplikace ASP.NET Core se stejným názvem jako předchozí projekt (*WebApp1*) tedy odpovídat oboru názvů na dva projekty.</span><span class="sxs-lookup"><span data-stu-id="fc52c-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="fc52c-120">S stejný obor názvů umožňuje snadnější zkopírovat kód mezi dva projekty.</span><span class="sxs-lookup"><span data-stu-id="fc52c-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="fc52c-121">Budete mít k vytvoření tohoto projektu do jiného adresáře než předchozí projekt, který používá stejný název.</span><span class="sxs-lookup"><span data-stu-id="fc52c-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Dialogové okno nového projektu](mvc/_static/new_core.png)

![Dialogové okno nové webové aplikace ASP.NET: Prázdná šablona projektu vybrané panelu šablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="fc52c-124">*Volitelné:* Vytvoření nové aplikace ASP.NET Core pomocí *webovou aplikaci* šablony projektu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="fc52c-125">Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivé uživatelské účty**.</span><span class="sxs-lookup"><span data-stu-id="fc52c-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="fc52c-126">Přejmenovat tuto aplikaci k *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="fc52c-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="fc52c-127">Vytvoření projektu ušetříte čas při převodu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="fc52c-128">Můžete si prohlédnout kód generovaný šablony najdete v článku konečný výsledek nebo zkopírujte kód do projektu převodu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="fc52c-129">Je také užitečné, pokud jste zaseknout se na krok převodu k porovnání s projektem šablona vytvořena.</span><span class="sxs-lookup"><span data-stu-id="fc52c-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="fc52c-130">Konfigurace lokality, aby používala MVC</span><span class="sxs-lookup"><span data-stu-id="fc52c-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="fc52c-131">Při cílení na .NET Core [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) se odkazuje ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="fc52c-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="fc52c-132">Tento balíček obsahuje balíčky běžně používané balíčky aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-132">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="fc52c-133">Pokud se zaměřujete na rozhraní .NET Framework, musí být odkazy na balíčky uvedené jednotlivě v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="fc52c-134">Při cílení na .NET Core [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) se odkazuje ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="fc52c-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="fc52c-135">Tento balíček obsahuje balíčky běžně používané balíčky aplikací MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="fc52c-136">Pokud se zaměřujete na rozhraní .NET Framework, musí být odkazy na balíčky uvedené jednotlivě v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="fc52c-137">Při cílení na .NET Core nebo .NET Framework, balíčků balíčky běžně používané podle aplikací MVC jsou jednotlivě uvedené v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="fc52c-138">`Microsoft.AspNetCore.Mvc` je rozhraní ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="fc52c-139">`Microsoft.AspNetCore.StaticFiles` je obslužná rutina statický soubor.</span><span class="sxs-lookup"><span data-stu-id="fc52c-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="fc52c-140">Modul runtime ASP.NET Core je modulární a musí se explicitně výslovný souhlas se doručování statických souborů (viz [statické soubory](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="fc52c-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="fc52c-141">Otevřít *Startup.cs* soubor a změňte kód tak, aby odpovídala následující:</span><span class="sxs-lookup"><span data-stu-id="fc52c-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="fc52c-142">`UseStaticFiles` – Metoda rozšíření přidá obslužnou rutinu statický soubor.</span><span class="sxs-lookup"><span data-stu-id="fc52c-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="fc52c-143">Jak už bylo zmíněno dříve, modul runtime ASP.NET je modulární a musí explicitně připojíte k doručování statických souborů.</span><span class="sxs-lookup"><span data-stu-id="fc52c-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="fc52c-144">`UseMvc` Přidá metody rozšíření směrování.</span><span class="sxs-lookup"><span data-stu-id="fc52c-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="fc52c-145">Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup) a [směrování](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="fc52c-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="fc52c-146">Přidat kontroler a zobrazení</span><span class="sxs-lookup"><span data-stu-id="fc52c-146">Add a controller and view</span></span>

<span data-ttu-id="fc52c-147">V této části přidáte minimální kontroler a zobrazení, která bude sloužit jako zástupné symboly pro kontroler ASP.NET MVC a zobrazení, že provedeme migraci v další části.</span><span class="sxs-lookup"><span data-stu-id="fc52c-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="fc52c-148">Přidat *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="fc52c-149">Přidat **třída Kontroleru rozhraní** s názvem *HomeController.cs* k *řadiče* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Přidat novou položku – dialogové okno](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="fc52c-151">Přidat *zobrazení* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="fc52c-152">Přidat *zobrazení Domů* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="fc52c-153">Přidat **zobrazení Razor** s názvem *Index.cshtml* k *zobrazení Domů* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Přidat novou položku – dialogové okno](mvc/_static/view.png)

<span data-ttu-id="fc52c-155">Struktura projektu je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="fc52c-155">The project structure is shown below:</span></span>

![Průzkumník řešení zobrazující soubory a složky WebApp1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="fc52c-157">Nahraďte obsah *Views/Home/Index.cshtml* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="fc52c-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="fc52c-158">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fc52c-158">Run the app.</span></span>

![Webová aplikace otevřít v Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="fc52c-160">Zobrazit [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview) Další informace.</span><span class="sxs-lookup"><span data-stu-id="fc52c-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="fc52c-161">Když teď máme minimální funkční projekt ASP.NET Core, můžeme začít migrace funkce z projektu ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="fc52c-162">Musíme přejít následující:</span><span class="sxs-lookup"><span data-stu-id="fc52c-162">We need to move the following:</span></span>

* <span data-ttu-id="fc52c-163">obsah na straně klienta (šablon stylů CSS, písem a skripty)</span><span class="sxs-lookup"><span data-stu-id="fc52c-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="fc52c-164">kontrolery</span><span class="sxs-lookup"><span data-stu-id="fc52c-164">controllers</span></span>

* <span data-ttu-id="fc52c-165">zobrazení</span><span class="sxs-lookup"><span data-stu-id="fc52c-165">views</span></span>

* <span data-ttu-id="fc52c-166">modely</span><span class="sxs-lookup"><span data-stu-id="fc52c-166">models</span></span>

* <span data-ttu-id="fc52c-167">Sdružování</span><span class="sxs-lookup"><span data-stu-id="fc52c-167">bundling</span></span>

* <span data-ttu-id="fc52c-168">filtry</span><span class="sxs-lookup"><span data-stu-id="fc52c-168">filters</span></span>

* <span data-ttu-id="fc52c-169">Protokol vstup a výstup, Identity (to se provádí v dalším kurzu).</span><span class="sxs-lookup"><span data-stu-id="fc52c-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="fc52c-170">Zobrazení a kontrolerů</span><span class="sxs-lookup"><span data-stu-id="fc52c-170">Controllers and views</span></span>

* <span data-ttu-id="fc52c-171">Kopírování všech metod v ASP.NET MVC `HomeController` k novému `HomeController`.</span><span class="sxs-lookup"><span data-stu-id="fc52c-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="fc52c-172">Všimněte si, že v architektuře ASP.NET MVC, návratový typ metody serveru integrovanou šablonu kontroleru akce [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); v ASP.NET Core MVC metody akce vrácené `IActionResult` místo.</span><span class="sxs-lookup"><span data-stu-id="fc52c-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="fc52c-173">`ActionResult` implementuje `IActionResult`, takže není nutné změnit návratový typ metody akce.</span><span class="sxs-lookup"><span data-stu-id="fc52c-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="fc52c-174">Kopírovat *About.cshtml*, *Contact.cshtml*, a *Index.cshtml* Razor zobrazit soubory z projektu ASP.NET MVC do projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc52c-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="fc52c-175">Spuštění aplikace ASP.NET Core a testování jednotlivých metod.</span><span class="sxs-lookup"><span data-stu-id="fc52c-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="fc52c-176">Jsme jste nemigrovali soubor rozložení nebo styly, takže vykreslené zobrazení obsahovat pouze obsah v zobrazení souborů.</span><span class="sxs-lookup"><span data-stu-id="fc52c-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="fc52c-177">Nebudete mít rozložení souboru vygenerovaného odkazy `About` a `Contact` zobrazení, takže budete mít k vyvolání z prohlížeče (nahradit **4492** se číslo portu používané ve vašem projektu).</span><span class="sxs-lookup"><span data-stu-id="fc52c-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Stránka kontaktu](mvc/_static/contact-page.png)

<span data-ttu-id="fc52c-179">Všimněte si, absenci stylů a položek nabídky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="fc52c-180">To Změníme v další části.</span><span class="sxs-lookup"><span data-stu-id="fc52c-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="fc52c-181">Statický obsah</span><span class="sxs-lookup"><span data-stu-id="fc52c-181">Static content</span></span>

<span data-ttu-id="fc52c-182">V předchozích verzích rozhraní ASP.NET MVC statický obsah hostovaný z kořenového adresáře webového projektu a byla smíšeného s soubory na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="fc52c-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="fc52c-183">V ASP.NET Core je statický obsah hostovaný v *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="fc52c-184">Budete chtít zkopírovat statický obsah ze staré aplikaci ASP.NET MVC *wwwroot* složky v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc52c-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="fc52c-185">V této ukázkové převodu:</span><span class="sxs-lookup"><span data-stu-id="fc52c-185">In this sample conversion:</span></span>

* <span data-ttu-id="fc52c-186">Kopírování *favicon.ico* soubor z původní projekt MVC tak, aby *wwwroot* složky v projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc52c-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="fc52c-187">Staré rozhraní ASP.NET MVC používá projekt [Bootstrap](https://getbootstrap.com/) stylů a úložišť spuštění souborů *obsahu* a *skripty* složek.</span><span class="sxs-lookup"><span data-stu-id="fc52c-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="fc52c-188">Šablona, která vygeneruje původního projektu ASP.NET MVC, odkazuje na spuštění v souboru rozložení (*Views/Shared/_Layout.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="fc52c-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="fc52c-189">Můžete zkopírovat *bootstrap.js* a *bootstrap.css* projektu soubory z technologie ASP.NET MVC *wwwroot* složky v novém projektu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="fc52c-190">Místo toho přidáme podporu Bootstrap (a dalších knihoven na straně klienta) pomocí CDN v další části.</span><span class="sxs-lookup"><span data-stu-id="fc52c-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="fc52c-191">Migrujte soubor rozložení</span><span class="sxs-lookup"><span data-stu-id="fc52c-191">Migrate the layout file</span></span>

* <span data-ttu-id="fc52c-192">Kopírovat *soubor _ViewStart.cshtml* souboru z původního projektu ASP.NET MVC *zobrazení* složku do projektu ASP.NET Core *zobrazení* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="fc52c-193">*Soubor _ViewStart.cshtml* nedošlo ke změně souboru v ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fc52c-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="fc52c-194">Vytvoření *zobrazení/Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="fc52c-195">*Volitelné:* Kopírování *_ViewImports.cshtml* z *FullAspNetCore* projekt MVC *zobrazení* složku do projektu ASP.NET Core *zobrazení* složka.</span><span class="sxs-lookup"><span data-stu-id="fc52c-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="fc52c-196">Odebrat všechny deklarace oboru názvů v *_ViewImports.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="fc52c-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="fc52c-197">*_ViewImports.cshtml* soubor obsahuje obory názvů pro všechny soubory, zobrazení a přináší [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="fc52c-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="fc52c-198">Pomocné rutiny značek se používají v novém souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="fc52c-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="fc52c-199">*_ViewImports.cshtml* souboru je nového v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc52c-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="fc52c-200">Kopírovat *_Layout.cshtml* souboru z původního projektu ASP.NET MVC *zobrazení/Shared* složku do projektu ASP.NET Core *zobrazení/Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="fc52c-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="fc52c-201">Otevřít *_Layout.cshtml* soubor a proveďte následující změny (dokončený kód je zobrazené dole):</span><span class="sxs-lookup"><span data-stu-id="fc52c-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="fc52c-202">Nahraďte `@Styles.Render("~/Content/css")` s `<link>` element načíst *bootstrap.css* (viz níže).</span><span class="sxs-lookup"><span data-stu-id="fc52c-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="fc52c-203">Odebrat `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="fc52c-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="fc52c-204">Okomentujte `@Html.Partial("_LoginPartial")` řádku (před a za řádek s `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="fc52c-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="fc52c-205">Další informace najdete v části [migrovat ověřování a identita pro ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="fc52c-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="fc52c-206">Nahraďte `@Scripts.Render("~/bundles/jquery")` s `<script>` – element (viz níže).</span><span class="sxs-lookup"><span data-stu-id="fc52c-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="fc52c-207">Nahraďte `@Scripts.Render("~/bundles/bootstrap")` s `<script>` – element (viz níže).</span><span class="sxs-lookup"><span data-stu-id="fc52c-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="fc52c-208">Nahrazení kódu pro zahrnutí CSS Bootstrapu:</span><span class="sxs-lookup"><span data-stu-id="fc52c-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="fc52c-209">Nahrazení značky jQuery a zahrnutí Bootstrap JavaScript:</span><span class="sxs-lookup"><span data-stu-id="fc52c-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="fc52c-210">Aktualizovaný *_Layout.cshtml* souboru je uveden níže:</span><span class="sxs-lookup"><span data-stu-id="fc52c-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="fc52c-211">Zobrazte webu v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="fc52c-211">View the site in the browser.</span></span> <span data-ttu-id="fc52c-212">Teď by měl správně načíst data pomocí očekávané styly na místě.</span><span class="sxs-lookup"><span data-stu-id="fc52c-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="fc52c-213">*Volitelné:* Můžete chtít zkuste použít nový soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="fc52c-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="fc52c-214">Pro tento projekt můžete zkopírovat soubor rozložení z *FullAspNetCore* projektu.</span><span class="sxs-lookup"><span data-stu-id="fc52c-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="fc52c-215">Nový soubor rozložení, který používá [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) a dalších vylepšení.</span><span class="sxs-lookup"><span data-stu-id="fc52c-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="fc52c-216">Konfigurace sdružování a minifikace</span><span class="sxs-lookup"><span data-stu-id="fc52c-216">Configure bundling and minification</span></span>

<span data-ttu-id="fc52c-217">Informace o tom, jak nakonfigurovat sdružování a minifikace najdete v tématu [sdružování a Minifikace](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="fc52c-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="fc52c-218">Řešení chyby HTTP 500</span><span class="sxs-lookup"><span data-stu-id="fc52c-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="fc52c-219">Existuje mnoho problémů, které může způsobit chybovou zprávu 500 protokolu HTTP, které neobsahují žádné informace o příčiny problému.</span><span class="sxs-lookup"><span data-stu-id="fc52c-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="fc52c-220">Například pokud *Views/_ViewImports.cshtml* soubor obsahuje obor názvů, který neexistuje v projektu, zobrazí se chyby HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="fc52c-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="fc52c-221">Ve výchozím nastavení aplikace ASP.NET Core `UseDeveloperExceptionPage` rozšíření se přidá do `IApplicationBuilder` a spuštěn, když je konfigurace *vývoj*.</span><span class="sxs-lookup"><span data-stu-id="fc52c-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="fc52c-222">To je podrobně popsán v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="fc52c-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="fc52c-223">Neošetřené výjimky ve webové aplikaci ASP.NET Core převede do chybové odpovědi protokolu HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="fc52c-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="fc52c-224">Za normálních okolností. Podrobnosti o chybě nejsou součástí tyto odpovědi, aby se zabránilo úniku citlivých informací o serveru.</span><span class="sxs-lookup"><span data-stu-id="fc52c-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="fc52c-225">V tématu **na stránce výjimek pro vývojáře** v [zpracování chyb](../fundamentals/error-handling.md) Další informace.</span><span class="sxs-lookup"><span data-stu-id="fc52c-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc52c-226">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fc52c-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
