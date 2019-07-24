---
title: Úvod do Razor Pages v ASP.NET Core
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 406e89c96ea63493091d0287077e244faee5f730
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308009"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="c99ad-103">Úvod do Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c99ad-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="c99ad-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="c99ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="c99ad-105">Razor Pages je nový aspekt ASP.NET Core MVC, který usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="c99ad-106">Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="c99ad-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="c99ad-107">Tento dokument představuje úvod do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c99ad-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="c99ad-108">Nejedná se o podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="c99ad-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="c99ad-109">Pokud některé části obsahují příliš pokročilé, přečtěte si téma Začínáme [s Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="c99ad-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="c99ad-110">Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="c99ad-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c99ad-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="c99ad-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c99ad-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c99ad-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c99ad-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c99ad-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c99ad-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c99ad-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="c99ad-115">Vytvoření projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c99ad-115">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c99ad-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c99ad-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c99ad-117">Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="c99ad-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c99ad-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c99ad-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c99ad-119">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-119">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c99ad-120">Spusťte `dotnet new razor` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-120">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="c99ad-121">Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="c99ad-121">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c99ad-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c99ad-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c99ad-123">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-123">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c99ad-124">Spusťte `dotnet new razor` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-124">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="c99ad-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c99ad-125">Razor Pages</span></span>

<span data-ttu-id="c99ad-126">Razor Pages je povolený v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c99ad-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="c99ad-127">Zvažte základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="c99ad-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="c99ad-128">Předchozí kód vypadá podobně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="c99ad-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="c99ad-129">To znamená, že tato `@page` direktiva je odlišná.</span><span class="sxs-lookup"><span data-stu-id="c99ad-129">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="c99ad-130">`@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler.</span><span class="sxs-lookup"><span data-stu-id="c99ad-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="c99ad-131">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="c99ad-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="c99ad-132">`@page`má vliv na chování jiných konstrukcí Razor.</span><span class="sxs-lookup"><span data-stu-id="c99ad-132">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="c99ad-133">Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="c99ad-133">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="c99ad-134">Soubor *Pages/Index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-134">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="c99ad-135">Model stránky *stránky/Index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-135">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="c99ad-136">Podle konvence `PageModel` má soubor třídy stejný název jako soubor stránky Razor s *příponou. cs* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-136">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="c99ad-137">Například předchozí stránka Razor je *Pages/Index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c99ad-137">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="c99ad-138">Soubor obsahující `PageModel` třídu má název Pages */Index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="c99ad-138">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="c99ad-139">Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="c99ad-139">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="c99ad-140">Následující tabulka ukazuje cestu stránky Razor a adresu URL pro porovnání:</span><span class="sxs-lookup"><span data-stu-id="c99ad-140">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="c99ad-141">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="c99ad-141">File name and path</span></span>               | <span data-ttu-id="c99ad-142">shodná adresa URL</span><span class="sxs-lookup"><span data-stu-id="c99ad-142">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="c99ad-143">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-143">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="c99ad-144">`/` Nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="c99ad-144">`/` or `/Index`</span></span> |
| <span data-ttu-id="c99ad-145">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-145">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="c99ad-146">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-146">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="c99ad-147">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-147">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="c99ad-148">`/Store` Nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="c99ad-148">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="c99ad-149">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="c99ad-149">Notes:</span></span>

* <span data-ttu-id="c99ad-150">Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-150">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="c99ad-151">`Index`je výchozí stránka, když adresa URL neobsahuje stránku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-151">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="c99ad-152">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="c99ad-152">Write a basic form</span></span>

<span data-ttu-id="c99ad-153">Razor Pages je navržený tak, aby se při vytváření aplikace usnadnily běžné vzory používané s webovými prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c99ad-153">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="c99ad-154">[Vázání modelů](xref:mvc/models/model-binding), [pomocníkům značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníkům, kteří *pracují pouze* s vlastnostmi definovanými ve třídě stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="c99ad-154">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="c99ad-155">Zvažte stránku, která pro `Contact` model implementuje základní formulář "kontaktujte nás":</span><span class="sxs-lookup"><span data-stu-id="c99ad-155">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="c99ad-156">Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .</span><span class="sxs-lookup"><span data-stu-id="c99ad-156">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="c99ad-157">Datový model:</span><span class="sxs-lookup"><span data-stu-id="c99ad-157">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="c99ad-158">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="c99ad-158">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="c99ad-159">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-159">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="c99ad-160">Model stránky *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-160">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="c99ad-161">Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="c99ad-161">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="c99ad-162">`PageModel` Třída umožňuje oddělení logiky stránky od její prezentace.</span><span class="sxs-lookup"><span data-stu-id="c99ad-162">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="c99ad-163">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-163">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="c99ad-164">Toto oddělení umožňuje spravovat závislosti stránek pomocí [vkládání závislostí](xref:fundamentals/dependency-injection) a [testování částí](xref:test/razor-pages-tests) stránky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-164">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="c99ad-165">Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle).</span><span class="sxs-lookup"><span data-stu-id="c99ad-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="c99ad-166">Můžete přidat metody obslužné rutiny pro libovolný příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="c99ad-166">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="c99ad-167">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="c99ad-167">The most common handlers are:</span></span>

* <span data-ttu-id="c99ad-168">`OnGet`pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="c99ad-169">Ukázka [OnGet](#OnGet)</span><span class="sxs-lookup"><span data-stu-id="c99ad-169">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="c99ad-170">`OnPost`pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="c99ad-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="c99ad-171">Přípona `Async` názvu je volitelná, ale často se používá v konvenci pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="c99ad-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="c99ad-172">`OnPostAsync` Kód v předchozím příkladu vypadá podobně jako při normálním zápisu do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c99ad-172">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="c99ad-173">Předchozí kód je typický pro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c99ad-173">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="c99ad-174">Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation)a výsledky akce, jsou sdílené.</span><span class="sxs-lookup"><span data-stu-id="c99ad-174">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="c99ad-175">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="c99ad-175">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="c99ad-176">Základní tok `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="c99ad-176">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="c99ad-177">Kontrola chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="c99ad-177">Check for validation errors.</span></span>

* <span data-ttu-id="c99ad-178">V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.</span><span class="sxs-lookup"><span data-stu-id="c99ad-178">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="c99ad-179">Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami.</span><span class="sxs-lookup"><span data-stu-id="c99ad-179">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="c99ad-180">Ověřování na straně klienta je stejné jako tradiční aplikace ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="c99ad-180">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="c99ad-181">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="c99ad-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="c99ad-182">Po úspěšném `OnPostAsync` zadání dat `RedirectToPage` zavolá metoda obslužné rutiny pomocnou metodu, `RedirectToPageResult`která vrátí instanci.</span><span class="sxs-lookup"><span data-stu-id="c99ad-182">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="c99ad-183">`RedirectToPage`je nový výsledek akce, který `RedirectToAction` se podobá nebo `RedirectToRoute`, ale přizpůsobený pro stránky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-183">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="c99ad-184">V předchozí ukázce přesměruje na stránku kořenového indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="c99ad-184">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="c99ad-185">`RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="c99ad-185">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="c99ad-186">Když odeslaný formulář obsahuje chyby ověřování (které jsou předány serveru),`OnPostAsync` metoda obslužné rutiny `Page` volá pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-186">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="c99ad-187">`Page`Vrátí instanci `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-187">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="c99ad-188">Vrácení `Page` se podobá tomu, jak vrátí `View`akce v řadičích.</span><span class="sxs-lookup"><span data-stu-id="c99ad-188">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="c99ad-189">`PageResult`je výchozí</span><span class="sxs-lookup"><span data-stu-id="c99ad-189">`PageResult` is the default</span></span> <!-- Review  --> <span data-ttu-id="c99ad-190">návratový typ pro metodu obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="c99ad-190">return type for a handler method.</span></span> <span data-ttu-id="c99ad-191">Metoda obslužné rutiny, `void` která vrací vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-191">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="c99ad-192">`Customer` Vlastnost používá`[BindProperty]` atribut pro přihlášení k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-192">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="c99ad-193">Razor Pages ve výchozím nastavení vlastnosti BIND pouze s jinými než`GET` slovesy.</span><span class="sxs-lookup"><span data-stu-id="c99ad-193">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="c99ad-194">Vazba na vlastnosti může snížit množství kódu, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="c99ad-194">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="c99ad-195">Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře (`<input asp-for="Customer.Name">`) a přijměte vstup.</span><span class="sxs-lookup"><span data-stu-id="c99ad-195">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="c99ad-196">Domovská stránka (*index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="c99ad-196">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="c99ad-197">Přidružená `PageModel` třída (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="c99ad-197">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="c99ad-198">Soubor *index. cshtml* obsahuje následující kód pro vytvoření odkazu pro úpravy pro každý kontakt:</span><span class="sxs-lookup"><span data-stu-id="c99ad-198">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="c99ad-199">[Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="c99ad-199">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="c99ad-200">Odkaz obsahuje data směrování s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-200">The link contains route data with the contact ID.</span></span> <span data-ttu-id="c99ad-201">Například, `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-201">For example, `http://localhost:5000/Edit/1`.</span></span> <span data-ttu-id="c99ad-202">`asp-area` Použijte atribut k určení oblasti.</span><span class="sxs-lookup"><span data-stu-id="c99ad-202">Use the `asp-area` attribute to specify an area.</span></span> <span data-ttu-id="c99ad-203">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="c99ad-203">For more information, see <xref:mvc/controllers/areas>.</span></span>

<span data-ttu-id="c99ad-204">Soubor *Pages/Edit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-204">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="c99ad-205">První řádek obsahuje `@page "{id:int}"` direktivu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-205">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="c99ad-206">Omezení`"{id:int}"` směrování instruuje stránku, aby přijímala požadavky na stránku, která `int` obsahuje data směrování.</span><span class="sxs-lookup"><span data-stu-id="c99ad-206">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="c99ad-207">Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int`, modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="c99ad-207">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="c99ad-208">Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="c99ad-208">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="c99ad-209">Soubor *Pages/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-209">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="c99ad-210">Soubor *index. cshtml* také obsahuje značky pro vytvoření tlačítka Odstranit pro kontaktování každého zákazníka:</span><span class="sxs-lookup"><span data-stu-id="c99ad-210">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="c99ad-211">Když je tlačítko Odstranit vykresleno ve formátu HTML, `formaction` obsahuje parametry pro:</span><span class="sxs-lookup"><span data-stu-id="c99ad-211">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="c99ad-212">ID kontaktu zákazníka určené `asp-route-id` atributem</span><span class="sxs-lookup"><span data-stu-id="c99ad-212">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="c99ad-213">`handler` Zadané`asp-page-handler` atributem.</span><span class="sxs-lookup"><span data-stu-id="c99ad-213">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="c99ad-214">Tady je příklad vygenerovaného tlačítka odstranit s ID `1`kontaktu zákazníka:</span><span class="sxs-lookup"><span data-stu-id="c99ad-214">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="c99ad-215">Když je vybráno tlačítko, pošle `POST` se na server požadavek na formulář.</span><span class="sxs-lookup"><span data-stu-id="c99ad-215">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="c99ad-216">Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu. `OnPost[handler]Async`</span><span class="sxs-lookup"><span data-stu-id="c99ad-216">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="c99ad-217">Vzhledem k tomu `delete` , že `OnPostDeleteAsync`jev tomto příkladu, metoda obslužné rutiny se `POST` používá ke zpracování požadavku. `handler`</span><span class="sxs-lookup"><span data-stu-id="c99ad-217">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="c99ad-218">Pokud je nastaven na jinou hodnotu, `remove`například, je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync`. `asp-page-handler`</span><span class="sxs-lookup"><span data-stu-id="c99ad-218">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="c99ad-219">`OnPostDeleteAsync` Metody:</span><span class="sxs-lookup"><span data-stu-id="c99ad-219">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="c99ad-220">Akceptuje `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-220">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="c99ad-221">Dotazuje databázi na kontaktování `FindAsync`zákazníka.</span><span class="sxs-lookup"><span data-stu-id="c99ad-221">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="c99ad-222">Pokud se kontakt zákazníka najde, odebere se ze seznamu kontaktů zákazníka.</span><span class="sxs-lookup"><span data-stu-id="c99ad-222">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="c99ad-223">Databáze je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="c99ad-223">The database is updated.</span></span>
* <span data-ttu-id="c99ad-224">Volání `RedirectToPage` pro přesměrování na stránku kořenového indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="c99ad-224">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="c99ad-225">Označit vlastnosti stránky jako povinné</span><span class="sxs-lookup"><span data-stu-id="c99ad-225">Mark page properties as required</span></span>

<span data-ttu-id="c99ad-226">Vlastnosti v `PageModel` lze dekorovat pomocí [požadovaného](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atributu:</span><span class="sxs-lookup"><span data-stu-id="c99ad-226">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="c99ad-227">Další informace najdete v tématu [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="c99ad-227">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="c99ad-228">Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet</span><span class="sxs-lookup"><span data-stu-id="c99ad-228">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="c99ad-229">`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="c99ad-229">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="c99ad-230">Na rozdíl `GET` od `HEAD` požadavků požadavky nevrátí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c99ad-230">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="c99ad-231">Obvykle je `HEAD` obslužná rutina vytvořena a volána pro požadavky: `OnHead`</span><span class="sxs-lookup"><span data-stu-id="c99ad-231">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="c99ad-232">V ASP.NET Core 2,1 nebo novější Razor Pages návrat k volání `OnGet` obslužné rutiny, pokud není definována žádná `OnHead` obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="c99ad-232">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="c99ad-233">Toto chování je povoleno voláním [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c99ad-233">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="c99ad-234">Výchozí šablony generují `SetCompatibilityVersion` volání v ASP.NET Core 2,1 a 2,2.</span><span class="sxs-lookup"><span data-stu-id="c99ad-234">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="c99ad-235">`SetCompatibilityVersion`efektivně nastaví možnost `AllowMappingHeadRequestsToGetHandler` Razor Pages na `true`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-235">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="c99ad-236">Místo toho, abyste se rozhodli pro všechna `SetCompatibilityVersion`chování pomocí, se můžete výslovně vyjádřit  ke konkrétnímu chování.</span><span class="sxs-lookup"><span data-stu-id="c99ad-236">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="c99ad-237">Následující kód výslovný v pro povolení `HEAD` mapování požadavků `OnGet` na obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="c99ad-237">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="c99ad-238">XSRF/CSRF a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c99ad-238">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="c99ad-239">Nemusíte psát žádný kód pro [ověřování](xref:security/anti-request-forgery)proti padělání.</span><span class="sxs-lookup"><span data-stu-id="c99ad-239">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="c99ad-240">Generování a ověření tokenu antipadělání jsou automaticky zahrnuty do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="c99ad-240">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="c99ad-241">Použití rozložení, částečných, šablon a značek pomocníků s Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c99ad-241">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="c99ad-242">Stránky fungují se všemi možnostmi modulu zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="c99ad-242">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="c99ad-243">Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*, *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="c99ad-243">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="c99ad-244">Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="c99ad-244">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c99ad-245">Přidat [stránku rozložení](xref:mvc/views/layout) na *stránky/Shared/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c99ad-245">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c99ad-246">Přidat [stránku rozložení](xref:mvc/views/layout) na *stránky/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c99ad-246">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="c99ad-247">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="c99ad-247">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="c99ad-248">Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).</span><span class="sxs-lookup"><span data-stu-id="c99ad-248">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="c99ad-249">Importuje struktury HTML, jako jsou JavaScript a StyleSheet.</span><span class="sxs-lookup"><span data-stu-id="c99ad-249">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="c99ad-250">Další informace najdete na [stránce rozložení](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="c99ad-250">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="c99ad-251">Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránce Pages/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c99ad-251">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c99ad-252">Rozložení se nachází na *stránkách nebo ve sdílené* složce.</span><span class="sxs-lookup"><span data-stu-id="c99ad-252">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="c99ad-253">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="c99ad-253">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="c99ad-254">Rozložení na *stránkách nebo ve sdílené* složce se dá použít na libovolné stránce Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-254">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="c99ad-255">Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-255">The layout file should go in the *Pages/Shared* folder.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="c99ad-256">Rozložení je ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-256">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="c99ad-257">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="c99ad-257">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="c99ad-258">Rozložení ve složce *stránky* lze použít na libovolné stránce Razor ve složce Pages.</span><span class="sxs-lookup"><span data-stu-id="c99ad-258">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

::: moniker-end

<span data-ttu-id="c99ad-259">Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-259">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="c99ad-260">*Zobrazení/Shared* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="c99ad-260">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="c99ad-261">Razor Pages mají spoléhat na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="c99ad-261">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="c99ad-262">Zobrazení hledání ze stránky Razor obsahuje složku *stránky* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-262">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="c99ad-263">Rozložení, šablony a částečně používané s řadiči MVC a konvenčními zobrazeními Razor *fungují pouze*.</span><span class="sxs-lookup"><span data-stu-id="c99ad-263">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="c99ad-264">Přidejte soubor *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-264">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="c99ad-265">`@namespace`je vysvětleno dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-265">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="c99ad-266">Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-266">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="c99ad-267">V případě explicitního použití direktivynastránce:`@namespace`</span><span class="sxs-lookup"><span data-stu-id="c99ad-267">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="c99ad-268">Direktiva nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-268">The directive sets the namespace for the page.</span></span> <span data-ttu-id="c99ad-269">`@model` Direktiva nemusí zahrnovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="c99ad-269">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="c99ad-270">Pokud je `@namespace` direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje direktivu. `@namespace`</span><span class="sxs-lookup"><span data-stu-id="c99ad-270">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="c99ad-271">Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-271">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="c99ad-272">Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="c99ad-272">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="c99ad-273">Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="c99ad-273">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="c99ad-274">Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` třída.</span><span class="sxs-lookup"><span data-stu-id="c99ad-274">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="c99ad-275">`@namespace`*funguje také se konvenčními zobrazeními Razor.*</span><span class="sxs-lookup"><span data-stu-id="c99ad-275">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="c99ad-276">Soubor zobrazení původní *stránky/vytvořit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-276">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="c99ad-277">Aktualizovaný soubor zobrazení *stránky/vytvoření. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c99ad-277">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="c99ad-278">[Projekt Razor Pages Starter](#rpvs17) obsahuje *stránku/_ValidationScriptsPartial. cshtml*, která se připojí k ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c99ad-278">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="c99ad-279">Další informace o částečných zobrazeních naleznete <xref:mvc/views/partial>v tématu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-279">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="c99ad-280">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="c99ad-280">URL generation for Pages</span></span>

<span data-ttu-id="c99ad-281">Stránka `Create` , která se zobrazuje dřív, `RedirectToPage`používá:</span><span class="sxs-lookup"><span data-stu-id="c99ad-281">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="c99ad-282">Aplikace má následující strukturu souborů nebo složek:</span><span class="sxs-lookup"><span data-stu-id="c99ad-282">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="c99ad-283">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="c99ad-283">*/Pages*</span></span>

  * <span data-ttu-id="c99ad-284">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-284">*Index.cshtml*</span></span>
  * <span data-ttu-id="c99ad-285">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="c99ad-285">*/Customers*</span></span>

    * <span data-ttu-id="c99ad-286">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-286">*Create.cshtml*</span></span>
    * <span data-ttu-id="c99ad-287">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-287">*Edit.cshtml*</span></span>
    * <span data-ttu-id="c99ad-288">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c99ad-288">*Index.cshtml*</span></span>

<span data-ttu-id="c99ad-289">*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit.* cshtml Pages přesměruje na *pages/index. cshtml* po úspěchu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-289">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="c99ad-290">Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce.</span><span class="sxs-lookup"><span data-stu-id="c99ad-290">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="c99ad-291">Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-291">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="c99ad-292">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c99ad-292">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="c99ad-293">Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` `/Index`(například).</span><span class="sxs-lookup"><span data-stu-id="c99ad-293">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="c99ad-294">Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkce, které se zakódujeme na adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c99ad-294">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="c99ad-295">Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="c99ad-295">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="c99ad-296">Generování adresy URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="c99ad-296">URL generation for pages supports relative names.</span></span> <span data-ttu-id="c99ad-297">Následující tabulka ukazuje, která stránka indexu je vybrána s různými `RedirectToPage` parametry ze *stránky/zákazníci/vytvořit. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="c99ad-297">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="c99ad-298">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="c99ad-298">RedirectToPage(x)</span></span>| <span data-ttu-id="c99ad-299">Page</span><span class="sxs-lookup"><span data-stu-id="c99ad-299">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="c99ad-300">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="c99ad-300">RedirectToPage("/Index")</span></span> | <span data-ttu-id="c99ad-301">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="c99ad-301">*Pages/Index*</span></span> |
| <span data-ttu-id="c99ad-302">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="c99ad-302">RedirectToPage("./Index");</span></span> | <span data-ttu-id="c99ad-303">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="c99ad-303">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="c99ad-304">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="c99ad-304">RedirectToPage("../Index")</span></span> | <span data-ttu-id="c99ad-305">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="c99ad-305">*Pages/Index*</span></span> |
| <span data-ttu-id="c99ad-306">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="c99ad-306">RedirectToPage("Index")</span></span>  | <span data-ttu-id="c99ad-307">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="c99ad-307">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="c99ad-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")` a`RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="c99ad-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="c99ad-309">Parametr je kombinován s cestou aktuální stránky k výpočtu názvu cílové stránky.  `RedirectToPage`</span><span class="sxs-lookup"><span data-stu-id="c99ad-309">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="c99ad-310">Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="c99ad-310">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="c99ad-311">Pokud k propojení mezi stránkami ve složce použijete relativní názvy, můžete tuto složku přejmenovat.</span><span class="sxs-lookup"><span data-stu-id="c99ad-311">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="c99ad-312">Všechny odkazy pořád fungují (protože neobsahují název složky).</span><span class="sxs-lookup"><span data-stu-id="c99ad-312">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c99ad-313">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="c99ad-313">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="c99ad-314">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="c99ad-314">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="c99ad-315">ViewData – atribut</span><span class="sxs-lookup"><span data-stu-id="c99ad-315">ViewData attribute</span></span>

<span data-ttu-id="c99ad-316">Data je možné předat na stránku pomocí [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="c99ad-316">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="c99ad-317">Vlastnosti na řadičích nebo modelech stránek Razor `[ViewData]` dekorované s jejich hodnotami uloženými a načtenými z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="c99ad-317">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="c99ad-318">V následujícím příkladu `AboutModel` `Title` obsahuje vlastnost dekorované pomocí `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-318">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="c99ad-319">`Title` Vlastnost je nastavena na název stránky o produktu:</span><span class="sxs-lookup"><span data-stu-id="c99ad-319">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="c99ad-320">Na stránce o aplikaci získáte přístup `Title` k vlastnosti jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="c99ad-320">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="c99ad-321">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="c99ad-321">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="c99ad-322">TempData</span><span class="sxs-lookup"><span data-stu-id="c99ad-322">TempData</span></span>

<span data-ttu-id="c99ad-323">ASP.NET Core zpřístupňuje vlastnost [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="c99ad-323">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="c99ad-324">Tato vlastnost ukládá data do jejich čtení.</span><span class="sxs-lookup"><span data-stu-id="c99ad-324">This property stores data until it's read.</span></span> <span data-ttu-id="c99ad-325">Metody `Keep` a`Peek` lze použít k prohlédnutí dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="c99ad-325">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="c99ad-326">`TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="c99ad-326">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="c99ad-327">`[TempData]` Atribut je v ASP.NET Core 2,0 nový a podporuje se na řadičích a stránkách.</span><span class="sxs-lookup"><span data-stu-id="c99ad-327">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="c99ad-328">Následující kód nastaví hodnotu `Message` pomocí: `TempData`</span><span class="sxs-lookup"><span data-stu-id="c99ad-328">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="c99ad-329">Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-329">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="c99ad-330">Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c99ad-330">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="c99ad-331">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="c99ad-331">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="c99ad-332">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="c99ad-332">Multiple handlers per page</span></span>

<span data-ttu-id="c99ad-333">Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="c99ad-333">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="c99ad-334">Formulář v předchozím příkladu obsahuje dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každá používá pro odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c99ad-334">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="c99ad-335">Atribut je doprovodný objekt k `asp-page`. `asp-page-handler`</span><span class="sxs-lookup"><span data-stu-id="c99ad-335">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="c99ad-336">`asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou.</span><span class="sxs-lookup"><span data-stu-id="c99ad-336">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="c99ad-337">`asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-337">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="c99ad-338">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="c99ad-338">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="c99ad-339">Předchozí kód používá *pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="c99ad-339">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="c99ad-340">Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="c99ad-340">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="c99ad-341">V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="c99ad-341">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="c99ad-342">Když jsou rutiny *post* a *Async* odebrány, názvy obslužných `JoinListUC`rutin jsou `JoinList` a.</span><span class="sxs-lookup"><span data-stu-id="c99ad-342">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="c99ad-343">Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` `http://localhost:5000/Customers/CreateFATH?handler=JoinList`kterou se odesílá,.</span><span class="sxs-lookup"><span data-stu-id="c99ad-343">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="c99ad-344">Cesta URL, na `OnPostJoinListUCAsync` kterou se `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`odesílá.</span><span class="sxs-lookup"><span data-stu-id="c99ad-344">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="c99ad-345">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="c99ad-345">Custom routes</span></span>

<span data-ttu-id="c99ad-346">Použijte tuto `@page` direktivu pro:</span><span class="sxs-lookup"><span data-stu-id="c99ad-346">Use the `@page` directive to:</span></span>

* <span data-ttu-id="c99ad-347">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="c99ad-347">Specify a custom route to a page.</span></span> <span data-ttu-id="c99ad-348">Například trasa na stránku o aplikaci `/Some/Other/Path` `@page "/Some/Other/Path"`může být nastavena na.</span><span class="sxs-lookup"><span data-stu-id="c99ad-348">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="c99ad-349">Připojit segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-349">Append segments to a page's default route.</span></span> <span data-ttu-id="c99ad-350">Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-350">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="c99ad-351">Připojení parametrů k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-351">Append parameters to a page's default route.</span></span> <span data-ttu-id="c99ad-352">Například parametr `id`ID může být požadován pro stránku s `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-352">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="c99ad-353">Je podporována kořenová relativní cesta, která je určena`~`vlnovkou () na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="c99ad-353">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="c99ad-354">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="c99ad-354">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="c99ad-355">Řetězec `?handler=JoinList` dotazu v adrese URL můžete změnit na segment `/JoinList` směrování zadáním šablony `@page "{handler?}"`trasy.</span><span class="sxs-lookup"><span data-stu-id="c99ad-355">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="c99ad-356">Pokud se vám řetězec `?handler=JoinList` dotazu v adrese URL nelíbí, můžete změnit trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="c99ad-356">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="c99ad-357">Trasu můžete přizpůsobit přidáním šablony trasy, která je uvedena v uvozovkách za `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="c99ad-357">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="c99ad-358">Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` `http://localhost:5000/Customers/CreateFATH/JoinList`kterou se odesílá,.</span><span class="sxs-lookup"><span data-stu-id="c99ad-358">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="c99ad-359">Cesta URL, na `OnPostJoinListUCAsync` kterou se `http://localhost:5000/Customers/CreateFATH/JoinListUC`odesílá.</span><span class="sxs-lookup"><span data-stu-id="c99ad-359">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="c99ad-360">`?` Následující`handler` údaj znamená, že parametr trasy je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="c99ad-360">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="c99ad-361">Konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="c99ad-361">Configuration and settings</span></span>

<span data-ttu-id="c99ad-362">Pokud chcete konfigurovat pokročilé možnosti, použijte metodu `AddRazorPagesOptions` rozšíření v Tvůrci MVC:</span><span class="sxs-lookup"><span data-stu-id="c99ad-362">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="c99ad-363">V `RazorPagesOptions` současné době můžete použít k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="c99ad-363">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="c99ad-364">Tímto způsobem v budoucnu povolíme lepší rozšíření.</span><span class="sxs-lookup"><span data-stu-id="c99ad-364">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="c99ad-365">Chcete-li předkompilovat zobrazení, přečtěte si téma [kompilace zobrazení Razor](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="c99ad-365">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="c99ad-366">[Stažení nebo zobrazení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="c99ad-366">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="c99ad-367">Další informace najdete v tématu [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start), který sestává z tohoto úvodního sestavení.</span><span class="sxs-lookup"><span data-stu-id="c99ad-367">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="c99ad-368">Zadejte, že Razor Pages jsou v kořenu obsahu.</span><span class="sxs-lookup"><span data-stu-id="c99ad-368">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="c99ad-369">Ve výchozím nastavení se Razor Pages rootem v adresáři */Pages* .</span><span class="sxs-lookup"><span data-stu-id="c99ad-369">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="c99ad-370">Přidejte [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete tak, že Razor Pages jsou v kořenovém adresáři obsahu ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:</span><span class="sxs-lookup"><span data-stu-id="c99ad-370">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="c99ad-371">Zadejte, že Razor Pages jsou ve vlastním kořenovém adresáři</span><span class="sxs-lookup"><span data-stu-id="c99ad-371">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="c99ad-372">Přidejte [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete tak, že vaše Razor Pages jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="c99ad-372">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="c99ad-373">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c99ad-373">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
