---
title: Úvod do Razor stránek v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak Razor stránky v ASP.NET Core zjednodušují a produktivnější vytváření kódu na stránce, než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 73af74c2cf65ec5e644af89c300ffa108825fb2e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404688"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="58aed-103">Úvod do Razor stránek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58aed-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58aed-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="58aed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="58aed-105">Stránky můžou vytvářet kódovací scénáře zaměřené na stránky a lépe a produktivnější než používání řadičů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="58aed-105"> Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="58aed-106">Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="58aed-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="58aed-107">Tento dokument popisuje úvodní Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="58aed-108">Nejedná se o podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="58aed-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="58aed-109">Pokud zjistíte, že některé části jsou moc pokročilé, přečtěte si téma Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="58aed-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="58aed-110">Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="58aed-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="58aed-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="58aed-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58aed-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58aed-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58aed-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58aed-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58aed-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="58aed-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="58aed-115">Vytvořit Razor projekt stránek</span><span class="sxs-lookup"><span data-stu-id="58aed-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58aed-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58aed-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58aed-117">Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="58aed-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58aed-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58aed-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58aed-119">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="58aed-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58aed-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="58aed-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58aed-121">Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="58aed-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="58aed-122">Stránky</span><span class="sxs-lookup"><span data-stu-id="58aed-122"> Pages</span></span>

Razor<span data-ttu-id="58aed-123">Stránky jsou povolené v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="58aed-123"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="58aed-124">Zvažte základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="58aed-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="58aed-125">Předchozí kód vypadá jako [ Razor soubor zobrazení](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="58aed-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="58aed-126">To znamená, že tato direktiva je odlišná [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="58aed-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="58aed-127">`@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler.</span><span class="sxs-lookup"><span data-stu-id="58aed-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="58aed-128">`@page`musí se jednat o první Razor direktivu na stránce.</span><span class="sxs-lookup"><span data-stu-id="58aed-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="58aed-129">`@page`má vliv na chování jiných [Razor](xref:mvc/views/razor) konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="58aed-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> Razor<span data-ttu-id="58aed-130">Názvy souborů stránek mají příponu *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58aed-130"> Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="58aed-131">Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="58aed-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="58aed-132">Soubor *Pages/Index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="58aed-133">Model stránky *stránky/Index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="58aed-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="58aed-134">Podle konvence `PageModel` má soubor třídy stejný název jako Razor stránkovací soubor s *příponou. cs* .</span><span class="sxs-lookup"><span data-stu-id="58aed-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="58aed-135">Například předchozí Razor stránka je *Pages/Index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="58aed-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="58aed-136">Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="58aed-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="58aed-137">Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="58aed-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="58aed-138">Následující tabulka ukazuje Razor cestu stránky a adresu URL pro porovnání:</span><span class="sxs-lookup"><span data-stu-id="58aed-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="58aed-139">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="58aed-139">File name and path</span></span>               | <span data-ttu-id="58aed-140">shodná adresa URL</span><span class="sxs-lookup"><span data-stu-id="58aed-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="58aed-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="58aed-142">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="58aed-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="58aed-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="58aed-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="58aed-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="58aed-146">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="58aed-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="58aed-147">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="58aed-147">Notes:</span></span>

* <span data-ttu-id="58aed-148">Modul runtime Razor ve výchozím nastavení vyhledá soubory stránek ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="58aed-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="58aed-149">`Index`je výchozí stránka, když adresa URL neobsahuje stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="58aed-150">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="58aed-150">Write a basic form</span></span>

Razor<span data-ttu-id="58aed-151">Stránky jsou navržené tak, aby při vytváření aplikace byly běžné vzory používané s webovými prohlížeči, které se dají snadno implementovat.</span><span class="sxs-lookup"><span data-stu-id="58aed-151"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="58aed-152">[Vazba modelů](xref:mvc/models/model-binding), [pomocníky značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníků pro HTML *stačí pracovat* s vlastnostmi definovanými ve Razor třídě stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="58aed-153">Zvažte stránku, která pro model implementuje základní formulář "kontaktujte nás" `Contact` :</span><span class="sxs-lookup"><span data-stu-id="58aed-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="58aed-154">Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .</span><span class="sxs-lookup"><span data-stu-id="58aed-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="58aed-155">Datový model:</span><span class="sxs-lookup"><span data-stu-id="58aed-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="58aed-156">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="58aed-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="58aed-157">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="58aed-158">Model stránky *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="58aed-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="58aed-159">Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="58aed-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="58aed-160">`PageModel`Třída umožňuje oddělení logiky stránky od její prezentace.</span><span class="sxs-lookup"><span data-stu-id="58aed-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="58aed-161">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="58aed-162">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="58aed-162">This separation allows:</span></span>

* <span data-ttu-id="58aed-163">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58aed-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="58aed-164">Testování částí</span><span class="sxs-lookup"><span data-stu-id="58aed-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="58aed-165">Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle).</span><span class="sxs-lookup"><span data-stu-id="58aed-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="58aed-166">Lze přidat metody obslužné rutiny pro jakýkoli příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="58aed-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="58aed-167">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="58aed-167">The most common handlers are:</span></span>

* <span data-ttu-id="58aed-168">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="58aed-169">V předchozím kódu `OnGet` Metoda zobrazí stránku *CreateModel. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="58aed-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="58aed-170">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="58aed-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="58aed-171">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="58aed-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="58aed-172">Předchozí kód je typický pro Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="58aed-173">Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="58aed-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="58aed-174">`OnPostAsync`Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.</span><span class="sxs-lookup"><span data-stu-id="58aed-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="58aed-175">Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation)a výsledky akce, fungují stejně jako řadiče a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="58aed-176">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="58aed-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="58aed-177">Základní tok `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="58aed-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="58aed-178">Kontrola chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="58aed-178">Check for validation errors.</span></span>

* <span data-ttu-id="58aed-179">V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.</span><span class="sxs-lookup"><span data-stu-id="58aed-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="58aed-180">Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami.</span><span class="sxs-lookup"><span data-stu-id="58aed-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="58aed-181">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="58aed-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="58aed-182">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="58aed-183">Vykreslený HTML ze *stránky/vytvořit. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58aed-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="58aed-184">V předchozím kódu vyúčtováním formuláře:</span><span class="sxs-lookup"><span data-stu-id="58aed-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="58aed-185">S platnými daty:</span><span class="sxs-lookup"><span data-stu-id="58aed-185">With valid data:</span></span>

  * <span data-ttu-id="58aed-186">`OnPostAsync`Metoda obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="58aed-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="58aed-187">`RedirectToPage` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="58aed-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="58aed-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="58aed-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="58aed-189">Je výsledkem akce.</span><span class="sxs-lookup"><span data-stu-id="58aed-189">Is an action result.</span></span>
    * <span data-ttu-id="58aed-190">Je podobný `RedirectToAction` nebo `RedirectToRoute` (používá se v řadičích a zobrazeních).</span><span class="sxs-lookup"><span data-stu-id="58aed-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="58aed-191">Je přizpůsoben pro stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-191">Is customized for pages.</span></span> <span data-ttu-id="58aed-192">V předchozí ukázce přesměruje na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="58aed-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="58aed-193">`RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="58aed-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="58aed-194">S chybami ověřování, které jsou předány serveru:</span><span class="sxs-lookup"><span data-stu-id="58aed-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="58aed-195">`OnPostAsync`Metoda obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="58aed-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="58aed-196">`Page` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="58aed-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="58aed-197">Vrácení `Page` se podobá tomu, jak vrátí akce v řadičích `View` .</span><span class="sxs-lookup"><span data-stu-id="58aed-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="58aed-198">`PageResult`je výchozí návratový typ pro metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="58aed-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="58aed-199">Metoda obslužné rutiny, která vrací `void` vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="58aed-200">V předchozím příkladu publikování formuláře bez výsledků hodnoty v [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) vrací hodnotu false.</span><span class="sxs-lookup"><span data-stu-id="58aed-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="58aed-201">V této ukázce nejsou v klientovi zobrazeny žádné chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="58aed-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="58aed-202">V tomto dokumentu se pojednává o chybách ověřování.</span><span class="sxs-lookup"><span data-stu-id="58aed-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="58aed-203">S chybami ověřování zjištěnými ověřováním na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="58aed-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="58aed-204">Data **nejsou** odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="58aed-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="58aed-205">Ověřování na straně klienta je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="58aed-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="58aed-206">`Customer`Vlastnost používá [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atribut pro výslovný souhlas s vazbou modelu:</span><span class="sxs-lookup"><span data-stu-id="58aed-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="58aed-207">`[BindProperty]`neměl **by se** používat pro modely obsahující vlastnosti, které by klient neměl měnit.</span><span class="sxs-lookup"><span data-stu-id="58aed-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="58aed-208">Další informace najdete v tématu [přestavení](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="58aed-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

Razor<span data-ttu-id="58aed-209">Stránky, ve výchozím nastavení vlastnosti BIND pouze bez `GET` slovesa.</span><span class="sxs-lookup"><span data-stu-id="58aed-209"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="58aed-210">Vazba na vlastnosti odstraní nutnost psaní kódu pro převod dat HTTP na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="58aed-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="58aed-211">Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře ( `<input asp-for="Customer.Name">` ) a přijměte vstup.</span><span class="sxs-lookup"><span data-stu-id="58aed-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="58aed-212">Prohlíží se soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="58aed-213">V předchozím kódu [Pomocník vstupní značky](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` váže `<input>` prvek HTML na `Customer.Name` výraz modelu.</span><span class="sxs-lookup"><span data-stu-id="58aed-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="58aed-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)zpřístupňuje pomocníkům značky.</span><span class="sxs-lookup"><span data-stu-id="58aed-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="58aed-215">Domovská stránka</span><span class="sxs-lookup"><span data-stu-id="58aed-215">The home page</span></span>

<span data-ttu-id="58aed-216">*Index. cshtml* je Domovská stránka:</span><span class="sxs-lookup"><span data-stu-id="58aed-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="58aed-217">Přidružená `PageModel` třída (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="58aed-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="58aed-218">Soubor *index. cshtml* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="58aed-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="58aed-219">`<a /a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="58aed-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="58aed-220">Odkaz obsahuje data směrování s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="58aed-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="58aed-221">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="58aed-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="58aed-222">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="58aed-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="58aed-223">Soubor *index. cshtml* obsahuje značky pro vytvoření tlačítka Odstranit pro každého kontaktu zákazníka:</span><span class="sxs-lookup"><span data-stu-id="58aed-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="58aed-224">Vykreslený kód HTML:</span><span class="sxs-lookup"><span data-stu-id="58aed-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="58aed-225">Pokud je tlačítko Odstranit vykresleno ve formátu HTML, obsahuje [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametry pro:</span><span class="sxs-lookup"><span data-stu-id="58aed-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="58aed-226">ID kontaktu zákazníka určené `asp-route-id` atributem.</span><span class="sxs-lookup"><span data-stu-id="58aed-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="58aed-227">`handler`, Určené `asp-page-handler` atributem.</span><span class="sxs-lookup"><span data-stu-id="58aed-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="58aed-228">Když je vybráno tlačítko, `POST` pošle se na server požadavek na formulář.</span><span class="sxs-lookup"><span data-stu-id="58aed-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="58aed-229">Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="58aed-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="58aed-230">Vzhledem k `handler` `delete` tomu, že je v tomto příkladu, `OnPostDeleteAsync` Metoda obslužné rutiny se používá ke zpracování `POST` požadavku.</span><span class="sxs-lookup"><span data-stu-id="58aed-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="58aed-231">Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove` , je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="58aed-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="58aed-232">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="58aed-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="58aed-233">Získá `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="58aed-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="58aed-234">Dotazuje databázi na kontaktování zákazníka `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="58aed-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="58aed-235">Pokud se kontakt zákazníka najde, odebere se a aktualizuje se databáze.</span><span class="sxs-lookup"><span data-stu-id="58aed-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="58aed-236">Volání <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pro přesměrování na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="58aed-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="58aed-237">Upravit soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="58aed-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="58aed-238">První řádek obsahuje `@page "{id:int}"` direktivu.</span><span class="sxs-lookup"><span data-stu-id="58aed-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="58aed-239">Omezení směrování `"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje `int` data směrování.</span><span class="sxs-lookup"><span data-stu-id="58aed-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="58aed-240">Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int` , modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="58aed-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="58aed-241">Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="58aed-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="58aed-242">Soubor *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="58aed-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="58aed-243">Ověřování</span><span class="sxs-lookup"><span data-stu-id="58aed-243">Validation</span></span>

<span data-ttu-id="58aed-244">Ověřovací pravidla:</span><span class="sxs-lookup"><span data-stu-id="58aed-244">Validation rules:</span></span>

* <span data-ttu-id="58aed-245">Jsou deklarativně určeny ve třídě modelu.</span><span class="sxs-lookup"><span data-stu-id="58aed-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="58aed-246">Jsou vynutily všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="58aed-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="58aed-247"><xref:System.ComponentModel.DataAnnotations>Obor názvů poskytuje sadu předdefinovaných ověřovacích atributů, které jsou aplikovány deklarativně na třídu nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="58aed-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="58aed-248">Tato dataanotace také obsahuje atributy formátování [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) , jako jsou tyto informace užitečné při formátování a neposkytují žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="58aed-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="58aed-249">Vezměte v úvahu `Customer` model:</span><span class="sxs-lookup"><span data-stu-id="58aed-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="58aed-250">Pomocí následujícího souboru zobrazení *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="58aed-251">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="58aed-251">The preceding code:</span></span>

* <span data-ttu-id="58aed-252">Zahrnuje skripty pro ověření jQuery a jQuery.</span><span class="sxs-lookup"><span data-stu-id="58aed-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="58aed-253">Používá `<div />` `<span />` [pomocníky značek](xref:mvc/views/tag-helpers/intro) a k povolení:</span><span class="sxs-lookup"><span data-stu-id="58aed-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="58aed-254">Ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="58aed-254">Client-side validation.</span></span>
  * <span data-ttu-id="58aed-255">Vykreslování chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="58aed-255">Validation error rendering.</span></span>

* <span data-ttu-id="58aed-256">Generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="58aed-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="58aed-257">Při publikování formuláře vytvořit bez hodnoty Name se zobrazí chybová zpráva "pole název je povinné."</span><span class="sxs-lookup"><span data-stu-id="58aed-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="58aed-258">ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="58aed-258">on the form.</span></span> <span data-ttu-id="58aed-259">Pokud je v klientovi povolený jazyk JavaScript, zobrazí se v prohlížeči chyba bez odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="58aed-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="58aed-260">`[StringLength(10)]`Atribut generuje `data-val-length-max="10"` na vykresleném HTML.</span><span class="sxs-lookup"><span data-stu-id="58aed-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="58aed-261">`data-val-length-max`zabrání prohlížečům v zadávání více než maximální zadané délky.</span><span class="sxs-lookup"><span data-stu-id="58aed-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="58aed-262">Pokud se k úpravě a přehrání příspěvku používá nástroj, například [Fiddler](https://www.telerik.com/fiddler) :</span><span class="sxs-lookup"><span data-stu-id="58aed-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="58aed-263">S názvem delším než 10.</span><span class="sxs-lookup"><span data-stu-id="58aed-263">With the name longer than 10.</span></span>
* <span data-ttu-id="58aed-264">Chybová zpráva "název pole musí být řetězec s maximální délkou 10."</span><span class="sxs-lookup"><span data-stu-id="58aed-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="58aed-265">je vrácen.</span><span class="sxs-lookup"><span data-stu-id="58aed-265">is returned.</span></span>

<span data-ttu-id="58aed-266">Vezměte v úvahu následující `Movie` model:</span><span class="sxs-lookup"><span data-stu-id="58aed-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="58aed-267">Atributy ověřování určují chování, které se má vyhovět pro vlastnosti modelu, na které se aplikují:</span><span class="sxs-lookup"><span data-stu-id="58aed-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="58aed-268">`Required`Atributy a `MinimumLength` označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.</span><span class="sxs-lookup"><span data-stu-id="58aed-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="58aed-269">`RegularExpression`Atribut slouží k omezení znaků, které lze zadat.</span><span class="sxs-lookup"><span data-stu-id="58aed-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="58aed-270">V předchozím kódu "Žánr":</span><span class="sxs-lookup"><span data-stu-id="58aed-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="58aed-271">Je nutné použít pouze písmena.</span><span class="sxs-lookup"><span data-stu-id="58aed-271">Must only use letters.</span></span>
  * <span data-ttu-id="58aed-272">První písmeno musí být velkými písmeny.</span><span class="sxs-lookup"><span data-stu-id="58aed-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="58aed-273">Mezery, číslice a speciální znaky nejsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="58aed-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="58aed-274">`RegularExpression`Hodnocení:</span><span class="sxs-lookup"><span data-stu-id="58aed-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="58aed-275">Vyžaduje, aby byl první znak velkým písmenem.</span><span class="sxs-lookup"><span data-stu-id="58aed-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="58aed-276">Umožňuje speciální znaky a čísla v následujících mezerách.</span><span class="sxs-lookup"><span data-stu-id="58aed-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="58aed-277">"PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="58aed-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="58aed-278">Atribut `Range` omezuje hodnotu v konkrétním rozsahu.</span><span class="sxs-lookup"><span data-stu-id="58aed-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="58aed-279">`StringLength`Atribut nastaví maximální délku řetězcové vlastnosti a volitelně její minimální délku.</span><span class="sxs-lookup"><span data-stu-id="58aed-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="58aed-280">Typy hodnot (například `decimal` , `int` , `float` , `DateTime` ) jsou podstatně požadovány a nepotřebují `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="58aed-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="58aed-281">Stránka vytvořit pro `Movie` model zobrazuje chyby s neplatnými hodnotami:</span><span class="sxs-lookup"><span data-stu-id="58aed-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="58aed-283">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="58aed-283">For more information, see:</span></span>

* [<span data-ttu-id="58aed-284">Přidání ověření do aplikace Movie</span><span class="sxs-lookup"><span data-stu-id="58aed-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="58aed-285">[Ověřování modelu v ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="58aed-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="58aed-286">Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet</span><span class="sxs-lookup"><span data-stu-id="58aed-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="58aed-287">`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="58aed-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="58aed-288">Na rozdíl od `GET` požadavků `HEAD` požadavky nevrátí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="58aed-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="58aed-289">Obvykle `OnHead` je obslužná rutina vytvořena a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="58aed-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor<span data-ttu-id="58aed-290">Stránky se vrátí k volání `OnGet` obslužné rutiny, `OnHead` Pokud není definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="58aed-290"> Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="58aed-291">XSRF/CSRF a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="58aed-291">XSRF/CSRF and Razor Pages</span></span>

Razor<span data-ttu-id="58aed-292">Stránky jsou chráněny [ověřováním proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="58aed-292"> Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="58aed-293">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny proti padělání do prvků formuláře HTML.</span><span class="sxs-lookup"><span data-stu-id="58aed-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="58aed-294">Použití rozložení, částečných, šablon a značek pomocníka se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="58aed-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="58aed-295">Stránky fungují se všemi možnostmi Razor modulu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="58aed-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="58aed-296">Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*a *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních Razor zobrazení.</span><span class="sxs-lookup"><span data-stu-id="58aed-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="58aed-297">Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="58aed-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="58aed-298">Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58aed-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="58aed-299">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="58aed-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="58aed-300">Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).</span><span class="sxs-lookup"><span data-stu-id="58aed-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="58aed-301">Importuje struktury HTML, jako jsou JavaScript a StyleSheet.</span><span class="sxs-lookup"><span data-stu-id="58aed-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="58aed-302">Obsah Razor stránky se vykreslí tam, kde `@RenderBody()` se volá.</span><span class="sxs-lookup"><span data-stu-id="58aed-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="58aed-303">Další informace najdete v tématu [rozložení stránky](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="58aed-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="58aed-304">Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58aed-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="58aed-305">Rozložení se nachází na *stránkách nebo ve sdílené* složce.</span><span class="sxs-lookup"><span data-stu-id="58aed-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="58aed-306">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="58aed-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="58aed-307">Rozložení na *stránkách nebo ve sdílené* složce můžete použít na libovolné Razor stránce ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="58aed-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="58aed-308">Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="58aed-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="58aed-309">Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="58aed-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="58aed-310">*Zobrazení/Shared* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="58aed-310">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="58aed-311">Stránky mají sloužit k spoléhání se na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="58aed-311"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="58aed-312">Zobrazení hledání ze Razor stránky obsahuje složku *stránky* .</span><span class="sxs-lookup"><span data-stu-id="58aed-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="58aed-313">Rozložení, šablony a částečné typy používané s řadiči MVC a konvenčními Razor zobrazeními *fungují pouze*.</span><span class="sxs-lookup"><span data-stu-id="58aed-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="58aed-314">Přidejte soubor *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="58aed-315">`@namespace`je vysvětleno dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="58aed-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="58aed-316">Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="58aed-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="58aed-317">`@namespace`Direktiva nastavená na stránce:</span><span class="sxs-lookup"><span data-stu-id="58aed-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="58aed-318">`@namespace`Direktiva nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="58aed-319">`@model`Direktiva nemusí zahrnovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="58aed-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="58aed-320">Pokud `@namespace` je direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu.</span><span class="sxs-lookup"><span data-stu-id="58aed-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="58aed-321">Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="58aed-322">Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="58aed-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="58aed-323">Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="58aed-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="58aed-324">Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` Třída.</span><span class="sxs-lookup"><span data-stu-id="58aed-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="58aed-325">`@namespace`*funguje také s konvenčními Razor zobrazeními.*</span><span class="sxs-lookup"><span data-stu-id="58aed-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="58aed-326">Zvažte *stránku zobrazení stránky/vytvořit soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="58aed-327">Aktualizovaný soubor zobrazení *Pages/Create. cshtml* s *_ViewImports. cshtml* a předchozím souborem rozložení:</span><span class="sxs-lookup"><span data-stu-id="58aed-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="58aed-328">V předchozím kódu *_ViewImports. cshtml* importovala obor názvů a pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="58aed-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="58aed-329">Soubor rozložení importoval soubory JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="58aed-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="58aed-330">[ Razor Úvodní projekt stránky](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které se připojí k ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="58aed-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="58aed-331">Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="58aed-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="58aed-332">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="58aed-332">URL generation for Pages</span></span>

<span data-ttu-id="58aed-333">`Create`Stránka, která se zobrazuje dřív, používá `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="58aed-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="58aed-334">Aplikace má následující strukturu souborů nebo složek:</span><span class="sxs-lookup"><span data-stu-id="58aed-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="58aed-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="58aed-335">*/Pages*</span></span>

  * <span data-ttu-id="58aed-336">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="58aed-337">*Soukromí. cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="58aed-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="58aed-338">*/Customers*</span></span>

    * <span data-ttu-id="58aed-339">*Vytvořit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="58aed-340">*Upravit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="58aed-341">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-341">*Index.cshtml*</span></span>

<span data-ttu-id="58aed-342">*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit. cshtml* Pages přesměruje na *Pages/Customers/index. cshtml* po úspěchu.</span><span class="sxs-lookup"><span data-stu-id="58aed-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="58aed-343">Řetězec `./Index` je relativní název stránky, který slouží k přístupu na předchozí stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="58aed-344">Slouží ke generování adres URL na stránce *Pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58aed-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="58aed-345">Například:</span><span class="sxs-lookup"><span data-stu-id="58aed-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="58aed-346">Absolutní název stránky `/Index` se používá ke generování adres URL na stránce *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58aed-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="58aed-347">Například:</span><span class="sxs-lookup"><span data-stu-id="58aed-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="58aed-348">Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="58aed-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="58aed-349">Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkční funkce v rámci hardwarového kódování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="58aed-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="58aed-350">Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="58aed-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="58aed-351">Generování adresy URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="58aed-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="58aed-352">Následující tabulka ukazuje, která stránka index je vybrána pomocí různých `RedirectToPage` parametrů na *stránkách/zákaznících/vytvořit. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="58aed-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="58aed-353">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="58aed-353">RedirectToPage(x)</span></span>| <span data-ttu-id="58aed-354">stránka</span><span class="sxs-lookup"><span data-stu-id="58aed-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="58aed-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="58aed-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="58aed-356">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-356">*Pages/Index*</span></span> |
| <span data-ttu-id="58aed-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="58aed-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="58aed-358">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="58aed-359">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="58aed-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="58aed-360">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-360">*Pages/Index*</span></span> |
| <span data-ttu-id="58aed-361">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="58aed-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="58aed-362">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="58aed-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="58aed-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="58aed-364">`RedirectToPage`Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="58aed-365">Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="58aed-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="58aed-366">Když se k propojení mezi stránkami ve složce použijí relativní názvy:</span><span class="sxs-lookup"><span data-stu-id="58aed-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="58aed-367">Přejmenování složky neruší relativní odkazy.</span><span class="sxs-lookup"><span data-stu-id="58aed-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="58aed-368">Odkazy nejsou přerušeny, protože neobsahují název složky.</span><span class="sxs-lookup"><span data-stu-id="58aed-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="58aed-369">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="58aed-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="58aed-370">Další informace naleznete v tématech <xref:mvc/controllers/areas> a <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="58aed-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="58aed-371">ViewData – atribut</span><span class="sxs-lookup"><span data-stu-id="58aed-371">ViewData attribute</span></span>

<span data-ttu-id="58aed-372">Data je možné předat stránce pomocí <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="58aed-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="58aed-373">Vlastnosti s `[ViewData]` atributem mají své hodnoty uložené a načtené z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="58aed-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="58aed-374">V následujícím příkladu `AboutModel` použije `[ViewData]` atribut na `Title` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="58aed-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="58aed-375">Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="58aed-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="58aed-376">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="58aed-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="58aed-377">TempData</span><span class="sxs-lookup"><span data-stu-id="58aed-377">TempData</span></span>

<span data-ttu-id="58aed-378">ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="58aed-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="58aed-379">Tato vlastnost ukládá data do jejich čtení.</span><span class="sxs-lookup"><span data-stu-id="58aed-379">This property stores data until it's read.</span></span> <span data-ttu-id="58aed-380"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Metody a <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> lze použít k prohlédnutí dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="58aed-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="58aed-381">`TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="58aed-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="58aed-382">Následující kód nastaví hodnotu `Message` pomocí `TempData` :</span><span class="sxs-lookup"><span data-stu-id="58aed-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="58aed-383">Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="58aed-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="58aed-384">Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="58aed-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="58aed-385">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="58aed-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="58aed-386">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="58aed-386">Multiple handlers per page</span></span>

<span data-ttu-id="58aed-387">Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="58aed-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="58aed-388">Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` pro odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="58aed-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="58aed-389">`asp-page-handler`Atribut je doprovodný objekt k `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="58aed-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="58aed-390">`asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou.</span><span class="sxs-lookup"><span data-stu-id="58aed-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="58aed-391">`asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="58aed-392">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="58aed-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="58aed-393">Předchozí kód používá *pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="58aed-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="58aed-394">Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="58aed-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="58aed-395">V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="58aed-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="58aed-396">Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="58aed-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="58aed-397">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="58aed-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="58aed-398">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="58aed-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="58aed-399">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="58aed-399">Custom routes</span></span>

<span data-ttu-id="58aed-400">Použijte tuto `@page` direktivu pro:</span><span class="sxs-lookup"><span data-stu-id="58aed-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="58aed-401">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-401">Specify a custom route to a page.</span></span> <span data-ttu-id="58aed-402">Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="58aed-403">Připojit segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-403">Append segments to a page's default route.</span></span> <span data-ttu-id="58aed-404">Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="58aed-405">Připojení parametrů k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="58aed-406">Například parametr ID `id` může být požadován pro stránku s `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="58aed-407">Je podporována kořenová relativní cesta, která je určena vlnovkou ( `~` ) na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="58aed-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="58aed-408">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="58aed-409">Pokud se v adrese URL nelíbí řetězec dotazu `?handler=JoinList` , změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="58aed-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="58aed-410">Trasa se dá přizpůsobit přidáním šablony trasy, která je uzavřená do dvojitých uvozovek za `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="58aed-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="58aed-411">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="58aed-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="58aed-412">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="58aed-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="58aed-413">`?`Následující `handler` údaj znamená, že parametr trasy je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="58aed-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="58aed-414">Pokročilá konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="58aed-414">Advanced configuration and settings</span></span>

<span data-ttu-id="58aed-415">Konfigurace a nastavení v následujících oddílech není pro většinu aplikací vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="58aed-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="58aed-416">Chcete-li konfigurovat pokročilé možnosti, použijte metodu rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> :</span><span class="sxs-lookup"><span data-stu-id="58aed-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="58aed-417">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> můžete nastavit kořenový adresář pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="58aed-418">Další informace o konvencích najdete v tématu [ Razor autorizační konvence stránek](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="58aed-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="58aed-419">Chcete-li předkompilovat zobrazení, přečtěte si téma [ Razor zobrazení kompilace](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="58aed-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="58aed-420">Zadejte, jestli Razor se stránky nacházejí v kořenu obsahu.</span><span class="sxs-lookup"><span data-stu-id="58aed-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="58aed-421">Ve výchozím nastavení Razor se stránky rootují v adresáři */Pages* .</span><span class="sxs-lookup"><span data-stu-id="58aed-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="58aed-422">Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> k určení, že vaše Razor stránky jsou v [kořenu obsahu](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) aplikace:</span><span class="sxs-lookup"><span data-stu-id="58aed-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="58aed-423">Zadejte, jestli Razor jsou stránky ve vlastním kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="58aed-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="58aed-424">Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> k určení, že Razor stránky jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="58aed-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="58aed-425">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58aed-425">Additional resources</span></span>

* <span data-ttu-id="58aed-426">Viz Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start), které jsou založeny na tomto úvodu.</span><span class="sxs-lookup"><span data-stu-id="58aed-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* <span data-ttu-id="58aed-427">[Autorizovat atributy a Razor stránky](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="58aed-427">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* [<span data-ttu-id="58aed-428">Stáhnout nebo zobrazit ukázkový kód</span><span class="sxs-lookup"><span data-stu-id="58aed-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58aed-429">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="58aed-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

Razor<span data-ttu-id="58aed-430">Stránky jsou novým aspektem ASP.NET Core MVC, který usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-430"> Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="58aed-431">Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="58aed-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="58aed-432">Tento dokument popisuje úvodní Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="58aed-433">Nejedná se o podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="58aed-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="58aed-434">Pokud zjistíte, že některé části jsou moc pokročilé, přečtěte si téma Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="58aed-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="58aed-435">Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="58aed-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="58aed-436">Požadavky</span><span class="sxs-lookup"><span data-stu-id="58aed-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58aed-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58aed-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58aed-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58aed-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58aed-439">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="58aed-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="58aed-440">Vytvořit Razor projekt stránek</span><span class="sxs-lookup"><span data-stu-id="58aed-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58aed-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58aed-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58aed-442">Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="58aed-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58aed-443">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="58aed-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="58aed-444">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="58aed-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="58aed-445">Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="58aed-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58aed-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58aed-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58aed-447">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="58aed-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a>Razor<span data-ttu-id="58aed-448">Stránky</span><span class="sxs-lookup"><span data-stu-id="58aed-448"> Pages</span></span>

Razor<span data-ttu-id="58aed-449">Stránky jsou povolené v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="58aed-449"> Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="58aed-450">Zvažte základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="58aed-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="58aed-451">Předchozí kód vypadá jako [ Razor soubor zobrazení](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="58aed-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="58aed-452">To znamená, že tato direktiva je odlišná `@page` .</span><span class="sxs-lookup"><span data-stu-id="58aed-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="58aed-453">`@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler.</span><span class="sxs-lookup"><span data-stu-id="58aed-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="58aed-454">`@page`musí se jednat o první Razor direktivu na stránce.</span><span class="sxs-lookup"><span data-stu-id="58aed-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="58aed-455">`@page`má vliv na chování jiných Razor konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="58aed-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="58aed-456">Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="58aed-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="58aed-457">Soubor *Pages/Index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="58aed-458">Model stránky *stránky/Index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="58aed-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="58aed-459">Podle konvence `PageModel` má soubor třídy stejný název jako Razor stránkovací soubor s *příponou. cs* .</span><span class="sxs-lookup"><span data-stu-id="58aed-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="58aed-460">Například předchozí Razor stránka je *Pages/Index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="58aed-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="58aed-461">Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="58aed-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="58aed-462">Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="58aed-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="58aed-463">Následující tabulka ukazuje Razor cestu stránky a adresu URL pro porovnání:</span><span class="sxs-lookup"><span data-stu-id="58aed-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="58aed-464">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="58aed-464">File name and path</span></span>               | <span data-ttu-id="58aed-465">shodná adresa URL</span><span class="sxs-lookup"><span data-stu-id="58aed-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="58aed-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="58aed-467">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="58aed-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="58aed-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="58aed-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="58aed-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="58aed-471">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="58aed-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="58aed-472">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="58aed-472">Notes:</span></span>

* <span data-ttu-id="58aed-473">Modul runtime Razor ve výchozím nastavení vyhledá soubory stránek ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="58aed-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="58aed-474">`Index`je výchozí stránka, když adresa URL neobsahuje stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="58aed-475">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="58aed-475">Write a basic form</span></span>

Razor<span data-ttu-id="58aed-476">Stránky jsou navržené tak, aby při vytváření aplikace byly běžné vzory používané s webovými prohlížeči, které se dají snadno implementovat.</span><span class="sxs-lookup"><span data-stu-id="58aed-476"> Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="58aed-477">[Vazba modelů](xref:mvc/models/model-binding), [pomocníky značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníků pro HTML *stačí pracovat* s vlastnostmi definovanými ve Razor třídě stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="58aed-478">Zvažte stránku, která pro model implementuje základní formulář "kontaktujte nás" `Contact` :</span><span class="sxs-lookup"><span data-stu-id="58aed-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="58aed-479">Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .</span><span class="sxs-lookup"><span data-stu-id="58aed-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="58aed-480">Datový model:</span><span class="sxs-lookup"><span data-stu-id="58aed-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="58aed-481">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="58aed-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="58aed-482">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="58aed-483">Model stránky *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="58aed-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="58aed-484">Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="58aed-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="58aed-485">`PageModel`Třída umožňuje oddělení logiky stránky od její prezentace.</span><span class="sxs-lookup"><span data-stu-id="58aed-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="58aed-486">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="58aed-487">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="58aed-487">This separation allows:</span></span>

* <span data-ttu-id="58aed-488">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="58aed-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="58aed-489">[Testování částí](xref:test/razor-pages-tests) stránky</span><span class="sxs-lookup"><span data-stu-id="58aed-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="58aed-490">Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle).</span><span class="sxs-lookup"><span data-stu-id="58aed-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="58aed-491">Můžete přidat metody obslužné rutiny pro jakýkoli příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="58aed-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="58aed-492">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="58aed-492">The most common handlers are:</span></span>

* <span data-ttu-id="58aed-493">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="58aed-494">Ukázka [OnGet](#OnGet)</span><span class="sxs-lookup"><span data-stu-id="58aed-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="58aed-495">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="58aed-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="58aed-496">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="58aed-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="58aed-497">Předchozí kód je typický pro Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="58aed-498">Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="58aed-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="58aed-499">`OnPostAsync`Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.</span><span class="sxs-lookup"><span data-stu-id="58aed-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="58aed-500">Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation), [ověřování](xref:mvc/models/validation)a výsledky akce, jsou sdílené.</span><span class="sxs-lookup"><span data-stu-id="58aed-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="58aed-501">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="58aed-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="58aed-502">Základní tok `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="58aed-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="58aed-503">Kontrola chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="58aed-503">Check for validation errors.</span></span>

* <span data-ttu-id="58aed-504">V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.</span><span class="sxs-lookup"><span data-stu-id="58aed-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="58aed-505">Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami.</span><span class="sxs-lookup"><span data-stu-id="58aed-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="58aed-506">Ověřování na straně klienta je stejné jako tradiční aplikace ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="58aed-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="58aed-507">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="58aed-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="58aed-508">Po úspěšném zadání dat `OnPostAsync` zavolá metoda obslužné rutiny `RedirectToPage` pomocnou metodu, která vrátí instanci `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="58aed-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="58aed-509">`RedirectToPage`je nový výsledek akce, který se podobá `RedirectToAction` nebo `RedirectToRoute` , ale přizpůsobený pro stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="58aed-510">V předchozí ukázce přesměruje na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="58aed-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="58aed-511">`RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="58aed-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="58aed-512">Když odeslaný formulář obsahuje chyby ověřování (které jsou předány serveru), `OnPostAsync` Metoda obslužné rutiny volá `Page` pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="58aed-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="58aed-513">`Page` vrací instanci `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="58aed-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="58aed-514">Vrácení `Page` se podobá tomu, jak vrátí akce v řadičích `View` .</span><span class="sxs-lookup"><span data-stu-id="58aed-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="58aed-515">`PageResult`je výchozí návratový typ pro metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="58aed-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="58aed-516">Metoda obslužné rutiny, která vrací `void` vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="58aed-517">`Customer`Vlastnost používá `[BindProperty]` atribut pro přihlášení k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="58aed-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor<span data-ttu-id="58aed-518">Stránky, ve výchozím nastavení vlastnosti BIND pouze bez `GET` slovesa.</span><span class="sxs-lookup"><span data-stu-id="58aed-518"> Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="58aed-519">Vazba na vlastnosti může snížit množství kódu, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="58aed-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="58aed-520">Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře ( `<input asp-for="Customer.Name">` ) a přijměte vstup.</span><span class="sxs-lookup"><span data-stu-id="58aed-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="58aed-521">Domovská stránka (*index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="58aed-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="58aed-522">Přidružená `PageModel` třída (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="58aed-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="58aed-523">Soubor *index. cshtml* obsahuje následující kód pro vytvoření odkazu pro úpravy pro každý kontakt:</span><span class="sxs-lookup"><span data-stu-id="58aed-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="58aed-524">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="58aed-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="58aed-525">Odkaz obsahuje data směrování s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="58aed-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="58aed-526">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="58aed-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="58aed-527">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="58aed-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="58aed-528">Pomocník značek je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="58aed-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="58aed-529">Soubor *Pages/Edit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="58aed-530">První řádek obsahuje `@page "{id:int}"` direktivu.</span><span class="sxs-lookup"><span data-stu-id="58aed-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="58aed-531">Omezení směrování `"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje `int` data směrování.</span><span class="sxs-lookup"><span data-stu-id="58aed-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="58aed-532">Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int` , modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="58aed-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="58aed-533">Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="58aed-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="58aed-534">Soubor *Pages/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="58aed-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="58aed-535">Soubor *index. cshtml* také obsahuje značky pro vytvoření tlačítka Odstranit pro kontaktování každého zákazníka:</span><span class="sxs-lookup"><span data-stu-id="58aed-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="58aed-536">Když je tlačítko Odstranit vykresleno ve formátu HTML, `formaction` obsahuje parametry pro:</span><span class="sxs-lookup"><span data-stu-id="58aed-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="58aed-537">ID kontaktu zákazníka určené `asp-route-id` atributem</span><span class="sxs-lookup"><span data-stu-id="58aed-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="58aed-538">`handler`Zadané `asp-page-handler` atributem.</span><span class="sxs-lookup"><span data-stu-id="58aed-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="58aed-539">Tady je příklad vygenerovaného tlačítka odstranit s ID kontaktu zákazníka `1` :</span><span class="sxs-lookup"><span data-stu-id="58aed-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="58aed-540">Když je vybráno tlačítko, `POST` pošle se na server požadavek na formulář.</span><span class="sxs-lookup"><span data-stu-id="58aed-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="58aed-541">Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="58aed-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="58aed-542">Vzhledem k `handler` `delete` tomu, že je v tomto příkladu, `OnPostDeleteAsync` Metoda obslužné rutiny se používá ke zpracování `POST` požadavku.</span><span class="sxs-lookup"><span data-stu-id="58aed-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="58aed-543">Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove` , je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="58aed-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="58aed-544">Následující kód ukazuje `OnPostDeleteAsync` obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="58aed-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="58aed-545">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="58aed-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="58aed-546">Akceptuje `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="58aed-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="58aed-547">Pokud direktiva stránky *index. cshtml* obsahuje omezení směrování `"{id:int?}"` , `id` pocházela z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="58aed-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="58aed-548">Data trasy pro `id` se zadává v identifikátoru URI, jako je například `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="58aed-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="58aed-549">Dotazuje databázi na kontaktování zákazníka `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="58aed-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="58aed-550">Pokud se kontakt zákazníka najde, odebere se ze seznamu kontaktů zákazníka.</span><span class="sxs-lookup"><span data-stu-id="58aed-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="58aed-551">Databáze je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="58aed-551">The database is updated.</span></span>
* <span data-ttu-id="58aed-552">Volání `RedirectToPage` pro přesměrování na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="58aed-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="58aed-553">Označit vlastnosti stránky jako povinné</span><span class="sxs-lookup"><span data-stu-id="58aed-553">Mark page properties as required</span></span>

<span data-ttu-id="58aed-554">Vlastnosti v `PageModel` lze označit [požadovaným](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atributem:</span><span class="sxs-lookup"><span data-stu-id="58aed-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="58aed-555">Další informace najdete v tématu [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="58aed-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="58aed-556">Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet</span><span class="sxs-lookup"><span data-stu-id="58aed-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="58aed-557">`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="58aed-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="58aed-558">Na rozdíl od `GET` požadavků `HEAD` požadavky nevrátí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="58aed-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="58aed-559">Obvykle `OnHead` je obslužná rutina vytvořena a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="58aed-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="58aed-560">V ASP.NET Core 2,1 nebo novějších se Razor stránky vrátí k volání `OnGet` obslužné rutiny, pokud `OnHead` není definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="58aed-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="58aed-561">Toto chování je povoleno voláním [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="58aed-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="58aed-562">Výchozí šablony generují `SetCompatibilityVersion` volání v ASP.NET Core 2,1 a 2,2.</span><span class="sxs-lookup"><span data-stu-id="58aed-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="58aed-563">`SetCompatibilityVersion`efektivně nastaví Razor možnost stránky `AllowMappingHeadRequestsToGetHandler` na `true` .</span><span class="sxs-lookup"><span data-stu-id="58aed-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="58aed-564">Místo toho, abyste se rozhodli pro všechna chování pomocí `SetCompatibilityVersion` , se můžete výslovně vyjádřit ke *konkrétnímu* chování.</span><span class="sxs-lookup"><span data-stu-id="58aed-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="58aed-565">Následující kód výslovný v pro povolení `HEAD` Mapování požadavků na `OnGet` obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="58aed-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="58aed-566">XSRF/CSRF a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="58aed-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="58aed-567">Nemusíte psát žádný kód pro [ověřování proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="58aed-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="58aed-568">Generování a ověření tokenu antipadělání jsou automaticky zahrnuty na Razor stránkách.</span><span class="sxs-lookup"><span data-stu-id="58aed-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="58aed-569">Použití rozložení, částečných, šablon a značek pomocníka se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="58aed-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="58aed-570">Stránky fungují se všemi možnostmi Razor modulu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="58aed-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="58aed-571">Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*, *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních Razor zobrazení.</span><span class="sxs-lookup"><span data-stu-id="58aed-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="58aed-572">Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="58aed-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="58aed-573">Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58aed-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="58aed-574">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="58aed-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="58aed-575">Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).</span><span class="sxs-lookup"><span data-stu-id="58aed-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="58aed-576">Importuje struktury HTML, jako jsou JavaScript a StyleSheet.</span><span class="sxs-lookup"><span data-stu-id="58aed-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="58aed-577">Další informace najdete na [stránce rozložení](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="58aed-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="58aed-578">Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58aed-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="58aed-579">Rozložení se nachází na *stránkách nebo ve sdílené* složce.</span><span class="sxs-lookup"><span data-stu-id="58aed-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="58aed-580">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="58aed-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="58aed-581">Rozložení na *stránkách nebo ve sdílené* složce můžete použít na libovolné Razor stránce ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="58aed-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="58aed-582">Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="58aed-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="58aed-583">Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="58aed-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="58aed-584">*Zobrazení/Shared* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="58aed-584">*Views/Shared* is an MVC views pattern.</span></span> Razor<span data-ttu-id="58aed-585">Stránky mají sloužit k spoléhání se na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="58aed-585"> Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="58aed-586">Zobrazení hledání ze Razor stránky obsahuje složku *stránky* .</span><span class="sxs-lookup"><span data-stu-id="58aed-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="58aed-587">Rozložení, šablony a částečné typy, které používáte s řadiči MVC a konvenčními Razor zobrazeními, *fungují pouze*.</span><span class="sxs-lookup"><span data-stu-id="58aed-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="58aed-588">Přidejte soubor *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="58aed-589">`@namespace`je vysvětleno dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="58aed-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="58aed-590">Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="58aed-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="58aed-591">V případě `@namespace` explicitního použití direktivy na stránce:</span><span class="sxs-lookup"><span data-stu-id="58aed-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="58aed-592">Direktiva nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="58aed-593">`@model`Direktiva nemusí zahrnovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="58aed-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="58aed-594">Pokud `@namespace` je direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu.</span><span class="sxs-lookup"><span data-stu-id="58aed-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="58aed-595">Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="58aed-596">Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="58aed-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="58aed-597">Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="58aed-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="58aed-598">Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` Třída.</span><span class="sxs-lookup"><span data-stu-id="58aed-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="58aed-599">`@namespace`*funguje také s konvenčními Razor zobrazeními.*</span><span class="sxs-lookup"><span data-stu-id="58aed-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="58aed-600">Soubor zobrazení původní *stránky/vytvořit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="58aed-601">Aktualizovaný soubor zobrazení *stránky/vytvoření. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="58aed-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="58aed-602">[ Razor Úvodní projekt stránky](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které se připojí k ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="58aed-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="58aed-603">Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="58aed-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="58aed-604">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="58aed-604">URL generation for Pages</span></span>

<span data-ttu-id="58aed-605">`Create`Stránka, která se zobrazuje dřív, používá `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="58aed-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="58aed-606">Aplikace má následující strukturu souborů nebo složek:</span><span class="sxs-lookup"><span data-stu-id="58aed-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="58aed-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="58aed-607">*/Pages*</span></span>

  * <span data-ttu-id="58aed-608">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="58aed-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="58aed-609">*/Customers*</span></span>

    * <span data-ttu-id="58aed-610">*Vytvořit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="58aed-611">*Upravit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="58aed-612">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="58aed-612">*Index.cshtml*</span></span>

<span data-ttu-id="58aed-613">*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit.* cshtml Pages přesměruje na *pages/index. cshtml* po úspěchu.</span><span class="sxs-lookup"><span data-stu-id="58aed-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="58aed-614">Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce.</span><span class="sxs-lookup"><span data-stu-id="58aed-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="58aed-615">Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="58aed-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="58aed-616">Například:</span><span class="sxs-lookup"><span data-stu-id="58aed-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="58aed-617">Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="58aed-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="58aed-618">Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkce, které se zakódujeme na adresu URL.</span><span class="sxs-lookup"><span data-stu-id="58aed-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="58aed-619">Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="58aed-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="58aed-620">Generování adresy URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="58aed-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="58aed-621">Následující tabulka ukazuje, která stránka indexu je vybrána s různými `RedirectToPage` parametry ze *stránky/zákazníci/vytvořit. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="58aed-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="58aed-622">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="58aed-622">RedirectToPage(x)</span></span>| <span data-ttu-id="58aed-623">stránka</span><span class="sxs-lookup"><span data-stu-id="58aed-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="58aed-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="58aed-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="58aed-625">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-625">*Pages/Index*</span></span> |
| <span data-ttu-id="58aed-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="58aed-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="58aed-627">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="58aed-628">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="58aed-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="58aed-629">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-629">*Pages/Index*</span></span> |
| <span data-ttu-id="58aed-630">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="58aed-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="58aed-631">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="58aed-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="58aed-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="58aed-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="58aed-633">`RedirectToPage`Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="58aed-634">Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="58aed-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="58aed-635">Pokud k propojení mezi stránkami ve složce použijete relativní názvy, můžete tuto složku přejmenovat.</span><span class="sxs-lookup"><span data-stu-id="58aed-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="58aed-636">Všechny odkazy pořád fungují (protože neobsahují název složky).</span><span class="sxs-lookup"><span data-stu-id="58aed-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="58aed-637">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="58aed-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="58aed-638">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="58aed-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="58aed-639">ViewData – atribut</span><span class="sxs-lookup"><span data-stu-id="58aed-639">ViewData attribute</span></span>

<span data-ttu-id="58aed-640">Data je možné předat na stránku pomocí [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="58aed-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="58aed-641">Vlastnosti v řadičích nebo Razor modelech stránek s `[ViewData]` atributem mají své hodnoty uložené a načtené z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="58aed-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="58aed-642">V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost s označením `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="58aed-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="58aed-643">`Title`Vlastnost je nastavena na název stránky o produktu:</span><span class="sxs-lookup"><span data-stu-id="58aed-643">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="58aed-644">Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="58aed-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="58aed-645">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="58aed-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="58aed-646">TempData</span><span class="sxs-lookup"><span data-stu-id="58aed-646">TempData</span></span>

<span data-ttu-id="58aed-647">ASP.NET Core zpřístupňuje vlastnost [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="58aed-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="58aed-648">Tato vlastnost ukládá data do jejich čtení.</span><span class="sxs-lookup"><span data-stu-id="58aed-648">This property stores data until it's read.</span></span> <span data-ttu-id="58aed-649">`Keep`Metody a `Peek` lze použít k prohlédnutí dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="58aed-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="58aed-650">`TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="58aed-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="58aed-651">Následující kód nastaví hodnotu `Message` pomocí `TempData` :</span><span class="sxs-lookup"><span data-stu-id="58aed-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="58aed-652">Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="58aed-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="58aed-653">Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="58aed-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="58aed-654">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="58aed-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="58aed-655">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="58aed-655">Multiple handlers per page</span></span>

<span data-ttu-id="58aed-656">Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="58aed-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="58aed-657">Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` pro odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="58aed-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="58aed-658">`asp-page-handler`Atribut je doprovodný objekt k `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="58aed-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="58aed-659">`asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou.</span><span class="sxs-lookup"><span data-stu-id="58aed-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="58aed-660">`asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="58aed-661">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="58aed-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="58aed-662">Předchozí kód používá *pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="58aed-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="58aed-663">Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="58aed-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="58aed-664">V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="58aed-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="58aed-665">Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="58aed-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="58aed-666">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="58aed-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="58aed-667">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="58aed-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="58aed-668">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="58aed-668">Custom routes</span></span>

<span data-ttu-id="58aed-669">Použijte tuto `@page` direktivu pro:</span><span class="sxs-lookup"><span data-stu-id="58aed-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="58aed-670">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="58aed-670">Specify a custom route to a page.</span></span> <span data-ttu-id="58aed-671">Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="58aed-672">Připojit segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-672">Append segments to a page's default route.</span></span> <span data-ttu-id="58aed-673">Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="58aed-674">Připojení parametrů k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="58aed-675">Například parametr ID `id` může být požadován pro stránku s `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="58aed-676">Je podporována kořenová relativní cesta, která je určena vlnovkou ( `~` ) na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="58aed-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="58aed-677">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="58aed-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="58aed-678">Pokud se v adrese URL nelíbí řetězec dotazu `?handler=JoinList` , změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="58aed-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="58aed-679">Trasa se dá přizpůsobit přidáním šablony trasy, která je uzavřená do dvojitých uvozovek za `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="58aed-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="58aed-680">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="58aed-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="58aed-681">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="58aed-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="58aed-682">`?`Následující `handler` údaj znamená, že parametr trasy je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="58aed-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="58aed-683">Konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="58aed-683">Configuration and settings</span></span>

<span data-ttu-id="58aed-684">Pokud chcete konfigurovat pokročilé možnosti, použijte metodu rozšíření `AddRazorPagesOptions` v Tvůrci MVC:</span><span class="sxs-lookup"><span data-stu-id="58aed-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="58aed-685">V současné době můžete použít `RazorPagesOptions` k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="58aed-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="58aed-686">Tímto způsobem v budoucnu povolíme lepší rozšíření.</span><span class="sxs-lookup"><span data-stu-id="58aed-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="58aed-687">Chcete-li předkompilovat zobrazení, přečtěte si téma [ Razor zobrazení kompilace](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="58aed-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="58aed-688">[Stažení nebo zobrazení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="58aed-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="58aed-689">Viz Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start), které jsou založeny na tomto úvodu.</span><span class="sxs-lookup"><span data-stu-id="58aed-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="58aed-690">Zadejte, jestli Razor se stránky nacházejí v kořenu obsahu.</span><span class="sxs-lookup"><span data-stu-id="58aed-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="58aed-691">Ve výchozím nastavení Razor se stránky rootují v adresáři */Pages* .</span><span class="sxs-lookup"><span data-stu-id="58aed-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="58aed-692">Přidejte [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, jestli Razor jsou vaše stránky v [kořenu obsahu](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:</span><span class="sxs-lookup"><span data-stu-id="58aed-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="58aed-693">Zadejte, jestli Razor jsou stránky ve vlastním kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="58aed-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="58aed-694">Přidejte [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , abyste určili, že vaše Razor stránky jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="58aed-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="58aed-695">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58aed-695">Additional resources</span></span>

* <span data-ttu-id="58aed-696">[Autorizovat atributy a Razor stránky](xref:security/authorization/simple#aarp)</span><span class="sxs-lookup"><span data-stu-id="58aed-696">[Authorize attribute and Razor Pages](xref:security/authorization/simple#aarp)</span></span>
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
