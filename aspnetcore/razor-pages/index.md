---
title: Úvod do služby v ASP.NET Core Razor Pages
author: Rick-Anderson
description: Zjistěte, jak v ASP.NET Core Razor Pages díky psaní kódu zaměřená na stránce scénáře jednodušší a produktivnější než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 419355d670536fef1a38fbcb8ce1fd880c0e9b0d
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555731"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="3de09-103">Úvod do služby v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3de09-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="3de09-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryanem Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="3de09-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="3de09-105">Stránky Razor je nový aspekt ASP.NET Core MVC, která provádí kódování zaměřené na stránce scénáře jednodušší a produktivnější.</span><span class="sxs-lookup"><span data-stu-id="3de09-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="3de09-106">Pokud hledáte kurz, který používá Model-View-Controller přístup, přečtěte si téma [Začínáme s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="3de09-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="3de09-107">Tento dokument obsahuje úvod do stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="3de09-108">Není podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="3de09-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="3de09-109">Pokud některé části příliš pokročilý najdete v tématu [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="3de09-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="3de09-110">Přehled ASP.NET Core, najdete v článku [Úvod do ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="3de09-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3de09-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3de09-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3de09-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3de09-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3de09-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3de09-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3de09-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3de09-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="3de09-115">Vytvoření projektu pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="3de09-115">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3de09-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3de09-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3de09-117">Zobrazit [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) podrobné pokyny o tom, jak vytvořit projekt stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3de09-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3de09-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3de09-119">Spustit `dotnet new webapp` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3de09-119">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3de09-120">Spustit `dotnet new razor` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3de09-120">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="3de09-121">Otevřete vygenerovaný *.csproj* souboru ze sady Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="3de09-121">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3de09-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3de09-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3de09-123">Spustit `dotnet new webapp` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3de09-123">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3de09-124">Spustit `dotnet new razor` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3de09-124">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="3de09-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3de09-125">Razor Pages</span></span>

<span data-ttu-id="3de09-126">Stránky Razor je povolený v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3de09-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="3de09-127">Vezměte v úvahu základní stránka: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="3de09-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="3de09-128">Předcházejícího kódu vypadá mnohem [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používané v aplikaci ASP.NET Core s kontrolerů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3de09-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="3de09-129">Co vlastně rozdíl je `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="3de09-129">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="3de09-130">`@page` Akce MVC – to znamená, že zpracovává požadavky přímo, bez nutnosti kontaktovat řadič vytvoří soubor.</span><span class="sxs-lookup"><span data-stu-id="3de09-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="3de09-131">`@page` musí být první direktivy Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="3de09-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="3de09-132">`@page` má vliv na chování jiných objektů, které Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-132">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="3de09-133">Podobná stránka, pomocí `PageModel` třídy, se zobrazí v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="3de09-133">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="3de09-134">*Pages/Index2.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="3de09-134">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="3de09-135">*Pages/Index2.cshtml.cs* model stránky:</span><span class="sxs-lookup"><span data-stu-id="3de09-135">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="3de09-136">Podle konvence `PageModel` soubor třídy má stejný název jako soubor stránky Razor s *.cs* připojí.</span><span class="sxs-lookup"><span data-stu-id="3de09-136">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="3de09-137">Je třeba na předchozí stránku Razor *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3de09-137">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="3de09-138">Soubor obsahující `PageModel` třída se nazývá *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="3de09-138">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="3de09-139">Přidružení cesty adresy URL na stránky jsou určeny na stránce umístění v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="3de09-139">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="3de09-140">Následující tabulka uvádí cesty stránky Razor a odpovídající adresy URL:</span><span class="sxs-lookup"><span data-stu-id="3de09-140">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="3de09-141">Název souboru a cestu</span><span class="sxs-lookup"><span data-stu-id="3de09-141">File name and path</span></span>               | <span data-ttu-id="3de09-142">odpovídající adresy URL</span><span class="sxs-lookup"><span data-stu-id="3de09-142">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3de09-143">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-143">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="3de09-144">`/` Nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="3de09-144">`/` or `/Index`</span></span> |
| <span data-ttu-id="3de09-145">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-145">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="3de09-146">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-146">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="3de09-147">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-147">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="3de09-148">`/Store` Nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="3de09-148">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="3de09-149">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="3de09-149">Notes:</span></span>

* <span data-ttu-id="3de09-150">Modul runtime vyhledá soubory Razor Pages *stránky* složky ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="3de09-150">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="3de09-151">`Index` Když neobsahuje adresu URL stránky, je výchozí stránka.</span><span class="sxs-lookup"><span data-stu-id="3de09-151">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="3de09-152">Zápis základní formulář</span><span class="sxs-lookup"><span data-stu-id="3de09-152">Write a basic form</span></span>

<span data-ttu-id="3de09-153">Stránky Razor je navržená tak, aby běžné vzory, které se používají u webových prohlížečů snadno se implementuje při sestavování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3de09-153">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="3de09-154">[Vazby modelu](xref:mvc/models/model-binding), [pomocných rutin značek](xref:mvc/views/tag-helpers/intro)a všechny pomocných rutin HTML *jenom pracovní* pomocí vlastnosti definované ve třídě stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-154">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="3de09-155">Vezměte v úvahu, který implementuje "kontaktujte nás" formuláře pro základní stránku `Contact` modelu:</span><span class="sxs-lookup"><span data-stu-id="3de09-155">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="3de09-156">Ukázky v tomto dokumentu `DbContext` se inicializuje [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) souboru.</span><span class="sxs-lookup"><span data-stu-id="3de09-156">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="3de09-157">Datový model:</span><span class="sxs-lookup"><span data-stu-id="3de09-157">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="3de09-158">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="3de09-158">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="3de09-159">*Pages/Create.cshtml* zobrazení souboru:</span><span class="sxs-lookup"><span data-stu-id="3de09-159">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="3de09-160">*Pages/Create.cshtml.cs* model stránky:</span><span class="sxs-lookup"><span data-stu-id="3de09-160">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="3de09-161">Podle konvence `PageModel` třída se nazývá `<PageName>Model` a je v oboru názvů stejný jako stránka.</span><span class="sxs-lookup"><span data-stu-id="3de09-161">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="3de09-162">`PageModel` Třída umožňuje oddělení logiky stránku od jeho prezentaci.</span><span class="sxs-lookup"><span data-stu-id="3de09-162">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="3de09-163">Definuje stránky obslužné rutiny pro požadavky odeslané na stránce a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="3de09-163">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="3de09-164">Toto oddělení vám umožní spravovat závislosti stránky prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection) a [Jednotkový test](xref:test/razor-pages-tests) stránky.</span><span class="sxs-lookup"><span data-stu-id="3de09-164">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="3de09-165">Na stránce `OnPostAsync` *metodu obslužné rutiny*, která se spouští `POST` požadavků (když uživatel odešle formulář).</span><span class="sxs-lookup"><span data-stu-id="3de09-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="3de09-166">Můžete přidat metody obslužné rutiny pro libovolný příkaz protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3de09-166">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="3de09-167">Nejčastěji používané rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="3de09-167">The most common handlers are:</span></span>

* <span data-ttu-id="3de09-168">`OnGet` Inicializace stavu potřebné pro stránku.</span><span class="sxs-lookup"><span data-stu-id="3de09-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="3de09-169">[OnGet](#OnGet) vzorku.</span><span class="sxs-lookup"><span data-stu-id="3de09-169">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="3de09-170">`OnPost` zpracování formulářů.</span><span class="sxs-lookup"><span data-stu-id="3de09-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="3de09-171">`Async` Přípona je volitelné, ale často používá konvence pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="3de09-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="3de09-172">`OnPostAsync` Kód v předchozím příkladu vypadá podobně jako byste normálně psaní v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3de09-172">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="3de09-173">Předchozí kód je typický pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-173">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="3de09-174">Většina primitiv MVC, jako jsou [vazby modelu](xref:mvc/models/model-binding), [ověření](xref:mvc/models/validation), výsledky akcí se sdílejí.</span><span class="sxs-lookup"><span data-stu-id="3de09-174">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="3de09-175">Předchozí `OnPostAsync` metody:</span><span class="sxs-lookup"><span data-stu-id="3de09-175">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3de09-176">Základní tok `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="3de09-176">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="3de09-177">Zkontrolován výskyt chyb ověření.</span><span class="sxs-lookup"><span data-stu-id="3de09-177">Check for validation errors.</span></span>

* <span data-ttu-id="3de09-178">Pokud nejsou žádné chyby, uložit data a přesměrování.</span><span class="sxs-lookup"><span data-stu-id="3de09-178">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="3de09-179">Pokud chyby existují, zobrazit stránku znovu pomocí ověřovacích zpráv.</span><span class="sxs-lookup"><span data-stu-id="3de09-179">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="3de09-180">Ověřování na straně klienta se shoduje s tradiční aplikace ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="3de09-180">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="3de09-181">V mnoha případech se chyby ověření by být zjištěn v klientském počítači a nikdy odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="3de09-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="3de09-182">Při zadání dat se úspěšně, `OnPostAsync` volání metody obslužné rutiny `RedirectToPage` Pomocná metoda vrátit instanci `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="3de09-182">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="3de09-183">`RedirectToPage` je nový výsledek akce, podobně jako `RedirectToAction` nebo `RedirectToRoute`, ale přizpůsobené pro stránky.</span><span class="sxs-lookup"><span data-stu-id="3de09-183">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="3de09-184">V předchozím příkladu, je přesměrován na indexovou stránku kořenové (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="3de09-184">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="3de09-185">`RedirectToPage` podrobně popisuje [generování adresy URL pro stránky](#url_gen) oddílu.</span><span class="sxs-lookup"><span data-stu-id="3de09-185">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="3de09-186">Když odeslané formuláře došlo k chybám ověřování, (, které se předá serveru),`OnPostAsync` volání metody obslužné rutiny `Page` Pomocná metoda.</span><span class="sxs-lookup"><span data-stu-id="3de09-186">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="3de09-187">`Page` Vrátí instanci `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="3de09-187">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="3de09-188">Vrací `Page` je podobný jak vrátit akce v řadiče `View`.</span><span class="sxs-lookup"><span data-stu-id="3de09-188">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="3de09-189">`PageResult` je výchozí nastavení</span><span class="sxs-lookup"><span data-stu-id="3de09-189">`PageResult` is the default</span></span> <!-- Review  --> <span data-ttu-id="3de09-190">Návratový typ pro metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3de09-190">return type for a handler method.</span></span> <span data-ttu-id="3de09-191">Metoda obslužné rutiny, která vrací `void` vykreslující danou stránku.</span><span class="sxs-lookup"><span data-stu-id="3de09-191">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="3de09-192">`Customer` Používá vlastnost `[BindProperty]` atribut můžete vyjádřit výslovný souhlas vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="3de09-192">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="3de09-193">Stránky Razor, ve výchozím nastavení, vázat vlastnosti pouze s příkazy bez GET.</span><span class="sxs-lookup"><span data-stu-id="3de09-193">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="3de09-194">Vytvoření vazby na vlastnosti může snížit množství kódu, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="3de09-194">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="3de09-195">Vazba snižuje kódu pomocí stejné vlastnosti k vykreslení pole formuláře (`<input asp-for="Customer.Name">`) a přijměte vstupu.</span><span class="sxs-lookup"><span data-stu-id="3de09-195">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="3de09-196">Na domovské stránce (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="3de09-196">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="3de09-197">Přidružené `PageModel` třídy (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3de09-197">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="3de09-198">*Index.cshtml* soubor obsahuje následující kód k vytvoření odkaz pro úpravy pro každého kontaktu:</span><span class="sxs-lookup"><span data-stu-id="3de09-198">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="3de09-199">[Ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použít `asp-route-{value}` atribut pro vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="3de09-199">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="3de09-200">Odkaz obsahuje data trasy s kontaktu ID.</span><span class="sxs-lookup"><span data-stu-id="3de09-200">The link contains route data with the contact ID.</span></span> <span data-ttu-id="3de09-201">Například, `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="3de09-201">For example, `http://localhost:5000/Edit/1`.</span></span> <span data-ttu-id="3de09-202">Použití `asp-area` atribut oblast.</span><span class="sxs-lookup"><span data-stu-id="3de09-202">Use the `asp-area` attribute to specify an area.</span></span> <span data-ttu-id="3de09-203">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="3de09-203">For more information, see <xref:mvc/controllers/areas>.</span></span>

<span data-ttu-id="3de09-204">*Pages/Edit.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="3de09-204">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="3de09-205">První řádek obsahuje `@page "{id:int}"` směrnice.</span><span class="sxs-lookup"><span data-stu-id="3de09-205">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="3de09-206">Omezení směrování`"{id:int}"` říká stránce tak, aby přijímal požadavky na stránku, které obsahují `int` směrovat data.</span><span class="sxs-lookup"><span data-stu-id="3de09-206">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="3de09-207">Pokud požadavek na stránku neobsahuje data trasy, který lze převést na `int`, modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="3de09-207">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3de09-208">Chcete-li nastavit ID volitelný, přidejte `?` pro dané omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="3de09-208">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3de09-209">*Pages/Edit.cshtml.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="3de09-209">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="3de09-210">*Index.cshtml* soubor obsahuje také značky, chcete-li vytvořit tlačítko pro odstranění pro každého zákazníka kontaktu:</span><span class="sxs-lookup"><span data-stu-id="3de09-210">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="3de09-211">Když tlačítko pro odstranění se vykreslí v HTML, jeho `formaction` obsahuje parametry pro:</span><span class="sxs-lookup"><span data-stu-id="3de09-211">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="3de09-212">Aby se zákazník obrátil zadané podle ID `asp-route-id` atribut.</span><span class="sxs-lookup"><span data-stu-id="3de09-212">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="3de09-213">`handler` Určená `asp-page-handler` atribut.</span><span class="sxs-lookup"><span data-stu-id="3de09-213">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="3de09-214">Tady je příklad tlačítko pro odstranění vykreslené zákazníků obraťte se na ID `1`:</span><span class="sxs-lookup"><span data-stu-id="3de09-214">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="3de09-215">Při výběru tlačítka, formulář `POST` odeslán požadavek na server.</span><span class="sxs-lookup"><span data-stu-id="3de09-215">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="3de09-216">Podle konvence je vybrán název metody obslužné rutiny založené na hodnotě `handler` parametr podle schéma `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="3de09-216">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="3de09-217">Vzhledem k tomu, `handler` je `delete` v tomto příkladu `OnPostDeleteAsync` obslužná rutina se používá k procesu `POST` požadavku.</span><span class="sxs-lookup"><span data-stu-id="3de09-217">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="3de09-218">Pokud `asp-page-handler` je nastavena na jinou hodnotu, jako například `remove`, metodu obslužné rutiny stránky s názvem `OnPostRemoveAsync` zaškrtnuto.</span><span class="sxs-lookup"><span data-stu-id="3de09-218">If the `asp-page-handler` is set to a different value, such as `remove`, a page handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="3de09-219">`OnPostDeleteAsync` Metody:</span><span class="sxs-lookup"><span data-stu-id="3de09-219">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="3de09-220">Přijímá `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="3de09-220">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="3de09-221">Dotazuje databázi pro kontakt zákazníka s `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="3de09-221">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="3de09-222">Pokud je nalezen kontakt zákazníka, budou odebráni ze seznamu kontaktů zákazníka.</span><span class="sxs-lookup"><span data-stu-id="3de09-222">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="3de09-223">Databáze se aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="3de09-223">The database is updated.</span></span>
* <span data-ttu-id="3de09-224">Volání `RedirectToPage` přesměrovat na indexovou stránku root (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="3de09-224">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="3de09-225">Označení vlastností stránky podle potřeby</span><span class="sxs-lookup"><span data-stu-id="3de09-225">Mark page properties as required</span></span>

<span data-ttu-id="3de09-226">Vlastnosti `PageModel` může být doplněny pomocí [vyžaduje](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atribut:</span><span class="sxs-lookup"><span data-stu-id="3de09-226">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="3de09-227">Další informace najdete v tématu [ověření modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3de09-227">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="manage-head-requests-with-the-onget-handler"></a><span data-ttu-id="3de09-228">Spravovat požadavky HEAD s obslužnou rutinou OnGet</span><span class="sxs-lookup"><span data-stu-id="3de09-228">Manage HEAD requests with the OnGet handler</span></span>

<span data-ttu-id="3de09-229">Požadavky HEAD umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="3de09-229">HEAD requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="3de09-230">Na rozdíl od požadavků GET HEAD požadavky nevracejte tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3de09-230">Unlike GET requests, HEAD requests don't return a response body.</span></span>

<span data-ttu-id="3de09-231">Obslužnou rutinu HEAD je obvykle vytvořen a volat pro požadavky HEAD:</span><span class="sxs-lookup"><span data-stu-id="3de09-231">Ordinarily, a HEAD handler is created and called for HEAD requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="3de09-232">Pokud žádná obslužná rutina HEAD (`OnHead`) je definovaný, stránky Razor spadne zpět na volání obslužné rutiny GET stránky (`OnGet`) v ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="3de09-232">If no HEAD handler (`OnHead`) is defined, Razor Pages falls back to calling the GET page handler (`OnGet`) in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="3de09-233">V ASP.NET Core 2.1 a 2.2 tomuto chování dochází u [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3de09-233">In ASP.NET Core 2.1 and 2.2, this behavior occurs with the [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.Configure`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="3de09-234">Generovat výchozí šablony `SetCompatibilityVersion` volání v ASP.NET Core 2.1 a 2.2.</span><span class="sxs-lookup"><span data-stu-id="3de09-234">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span>

<span data-ttu-id="3de09-235">`SetCompatibilityVersion` Nastavuje možnost Razor Pages `AllowMappingHeadRequestsToGetHandler` k `true`.</span><span class="sxs-lookup"><span data-stu-id="3de09-235">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="3de09-236">Místo výběru všech 2.1 chování s `SetCompatibilityVersion`, je můžete explicitně vyjádřit výslovný souhlas pro konkrétní chování.</span><span class="sxs-lookup"><span data-stu-id="3de09-236">Rather than opting into all 2.1 behaviors with `SetCompatibilityVersion`, you can explicitly opt-in to specific behaviors.</span></span> <span data-ttu-id="3de09-237">Požádá o následující kód do Požadavky HEAD mapování na obslužnou rutinu GET.</span><span class="sxs-lookup"><span data-stu-id="3de09-237">The following code opts into the mapping HEAD requests to the GET handler.</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="3de09-238">XSRF/CSRF a stránky Razor</span><span class="sxs-lookup"><span data-stu-id="3de09-238">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="3de09-239">Nemusíte psát kód pro [antiforgery ověření](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="3de09-239">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="3de09-240">Antiforgery generování tokenů a ověření jsou automaticky součástí stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-240">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="3de09-241">Pomocí rozložení, částečných zobrazení, šablon a pomocných rutin značek se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="3de09-241">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="3de09-242">Stránky fungovat se všemi funkcemi zobrazovací modul Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-242">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="3de09-243">Rozložení, částečných zobrazení, šablony, pomocných rutin značek *soubor _ViewStart.cshtml*, *_ViewImports.cshtml* pracovat stejným způsobem, jako je tomu u konvenční zobrazení syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-243">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="3de09-244">Tato stránka umožňuje declutter s využitím některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="3de09-244">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3de09-245">Přidat [stránku rozložení](xref:mvc/views/layout) k *Pages/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3de09-245">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3de09-246">Přidat [stránku rozložení](xref:mvc/views/layout) k *Pages/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3de09-246">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="3de09-247">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="3de09-247">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="3de09-248">Určuje rozložení jednotlivých stránek (Pokud na stránce výslovný rozložení).</span><span class="sxs-lookup"><span data-stu-id="3de09-248">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="3de09-249">Importuje HTML struktury, jako jsou JavaScript a šablony stylů.</span><span class="sxs-lookup"><span data-stu-id="3de09-249">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="3de09-250">Zobrazit [stránku rozložení](xref:mvc/views/layout) Další informace.</span><span class="sxs-lookup"><span data-stu-id="3de09-250">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="3de09-251">[Rozložení](xref:mvc/views/layout#specifying-a-layout) je nastavena *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3de09-251">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3de09-252">Rozložení se *stránek/Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-252">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="3de09-253">Stránky vyhledejte jiným zobrazením (rozložení, šablony, částečných zobrazení) hierarchicky, spouští se ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="3de09-253">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="3de09-254">Rozložení v *stránek/Shared* složky je možné z jakékoli stránky Razor pod *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-254">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="3de09-255">Soubor rozložení by měl Přejít *stránek/Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-255">The layout file should go in the *Pages/Shared* folder.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3de09-256">Rozložení se *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-256">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="3de09-257">Stránky vyhledejte jiným zobrazením (rozložení, šablony, částečných zobrazení) hierarchicky, spouští se ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="3de09-257">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="3de09-258">Rozložení v *stránky* složky je možné z jakékoli stránky Razor pod *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-258">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

::: moniker-end

<span data-ttu-id="3de09-259">Doporučujeme **není** vložit ho do rozložení *zobrazení/Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-259">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="3de09-260">*Zobrazení/Shared* je vzorek zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="3de09-260">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="3de09-261">Spoléhat na hierarchii složek, ne cestu konvence jsou určené pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3de09-261">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="3de09-262">Obsahuje zobrazení hledání ze stránky Razor *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-262">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="3de09-263">Rozložení, šablony a částečných zobrazení, které používáte s konvenčním Razor zobrazení a kontrolery MVC *jenom pracovní*.</span><span class="sxs-lookup"><span data-stu-id="3de09-263">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="3de09-264">Přidat *Pages/_ViewImports.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="3de09-264">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="3de09-265">`@namespace` je vysvětlen později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="3de09-265">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="3de09-266">`@addTagHelper` – Direktiva přináší [integrovaných pomocných rutin značek](xref:mvc/views/tag-helpers/builtin-th/Index) na všechny stránky v *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="3de09-266">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="3de09-267">Když `@namespace` explicitně použít direktiva na stránce:</span><span class="sxs-lookup"><span data-stu-id="3de09-267">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="3de09-268">Direktiva Nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="3de09-268">The directive sets the namespace for the page.</span></span> <span data-ttu-id="3de09-269">`@model` – Direktiva není nutné, aby obsahoval obor názvů.</span><span class="sxs-lookup"><span data-stu-id="3de09-269">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="3de09-270">Když `@namespace` – direktiva je obsažen v *_ViewImports.cshtml*, určený obor názvů poskytuje předponu pro obor názvů generovaného na stránce, která importuje `@namespace` – direktiva.</span><span class="sxs-lookup"><span data-stu-id="3de09-270">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="3de09-271">Zbytek vygenerovaného oboru názvů (přípona) je relativní cesta mezi složku obsahující tečkou oddělená *_ViewImports.cshtml* a složku, která obsahuje na stránce.</span><span class="sxs-lookup"><span data-stu-id="3de09-271">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="3de09-272">Například `PageModel` třídy *Pages/Customers/Edit.cshtml.cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="3de09-272">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="3de09-273">*Pages/_ViewImports.cshtml* souboru nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="3de09-273">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="3de09-274">Vygenerovaný obor názvů pro *Pages/Customers/Edit.cshtml* stránky Razor je stejný jako `PageModel` třídy.</span><span class="sxs-lookup"><span data-stu-id="3de09-274">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="3de09-275">`@namespace` *taky spolupracuje s konvenčním zobrazení syntaxe Razor.*</span><span class="sxs-lookup"><span data-stu-id="3de09-275">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="3de09-276">Původní *Pages/Create.cshtml* zobrazení souboru:</span><span class="sxs-lookup"><span data-stu-id="3de09-276">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="3de09-277">Aktualizovaný *Pages/Create.cshtml* zobrazení souboru:</span><span class="sxs-lookup"><span data-stu-id="3de09-277">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="3de09-278">[Razor Pages starter projektu](#rpvs17) obsahuje *Pages/_ValidationScriptsPartial.cshtml*, který zachytí nahoru ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3de09-278">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="3de09-279">Další informace o částečné zobrazení, naleznete v tématu <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="3de09-279">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="3de09-280">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="3de09-280">URL generation for Pages</span></span>

<span data-ttu-id="3de09-281">`Create` Stránky uvedenému výše používá `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="3de09-281">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="3de09-282">Aplikace má následující strukturu souboru nebo složky:</span><span class="sxs-lookup"><span data-stu-id="3de09-282">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="3de09-283">*/ Stránky*</span><span class="sxs-lookup"><span data-stu-id="3de09-283">*/Pages*</span></span>

  * <span data-ttu-id="3de09-284">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-284">*Index.cshtml*</span></span>
  * <span data-ttu-id="3de09-285">*/ Zákazníků*</span><span class="sxs-lookup"><span data-stu-id="3de09-285">*/Customers*</span></span>

    * <span data-ttu-id="3de09-286">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-286">*Create.cshtml*</span></span>
    * <span data-ttu-id="3de09-287">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-287">*Edit.cshtml*</span></span>
    * <span data-ttu-id="3de09-288">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3de09-288">*Index.cshtml*</span></span>

<span data-ttu-id="3de09-289">*Pages/Customers/Create.cshtml* a *Pages/Customers/Edit.cshtml* stránek přesměrování na *Pages/Index.cshtml* po úspěšném provedení.</span><span class="sxs-lookup"><span data-stu-id="3de09-289">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="3de09-290">Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce.</span><span class="sxs-lookup"><span data-stu-id="3de09-290">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="3de09-291">Řetězec `/Index` slouží ke generování identifikátorů URI pro *Pages/Index.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="3de09-291">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="3de09-292">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3de09-292">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="3de09-293">Název stránky je cesta ke stránce z kořenového adresáře */stránky* složku včetně úvodní `/` (například `/Index`).</span><span class="sxs-lookup"><span data-stu-id="3de09-293">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="3de09-294">Předchozí generace vzorky adresy URL umožňují rozšířené možnosti a funkční možnosti nad hardcoding adresy URL.</span><span class="sxs-lookup"><span data-stu-id="3de09-294">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="3de09-295">Generování adresy URL používá [směrování](xref:mvc/controllers/routing) a může generovat a kódování parametry podle v cílové cestě bude definován takhle trasy.</span><span class="sxs-lookup"><span data-stu-id="3de09-295">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="3de09-296">Generování adresy URL pro stránky podporuje relativních názvů.</span><span class="sxs-lookup"><span data-stu-id="3de09-296">URL generation for pages supports relative names.</span></span> <span data-ttu-id="3de09-297">V následující tabulce jsou uvedeny je vybrána na stránce, které Index s různými `RedirectToPage` parametry z *Pages/Customers/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3de09-297">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="3de09-298">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="3de09-298">RedirectToPage(x)</span></span>| <span data-ttu-id="3de09-299">Stránka</span><span class="sxs-lookup"><span data-stu-id="3de09-299">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3de09-300">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="3de09-300">RedirectToPage("/Index")</span></span> | <span data-ttu-id="3de09-301">*Stránky/indexu*</span><span class="sxs-lookup"><span data-stu-id="3de09-301">*Pages/Index*</span></span> |
| <span data-ttu-id="3de09-302">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="3de09-302">RedirectToPage("./Index");</span></span> | <span data-ttu-id="3de09-303">*Stránky/zákazníci/indexu*</span><span class="sxs-lookup"><span data-stu-id="3de09-303">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="3de09-304">RedirectToPage(".. / Index")</span><span class="sxs-lookup"><span data-stu-id="3de09-304">RedirectToPage("../Index")</span></span> | <span data-ttu-id="3de09-305">*Stránky/indexu*</span><span class="sxs-lookup"><span data-stu-id="3de09-305">*Pages/Index*</span></span> |
| <span data-ttu-id="3de09-306">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="3de09-306">RedirectToPage("Index")</span></span>  | <span data-ttu-id="3de09-307">*Stránky/zákazníci/indexu*</span><span class="sxs-lookup"><span data-stu-id="3de09-307">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="3de09-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="3de09-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="3de09-309">`RedirectToPage` Parametr *kombinovat* s cestou k aktuální stránky pro výpočet název cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="3de09-309">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="3de09-310">Relativní název propojení je užitečné, když vytváření webů s komplexní strukturou.</span><span class="sxs-lookup"><span data-stu-id="3de09-310">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="3de09-311">Pokud používáte relativní názvy propojení mezi stránkami ve složce, můžete přejmenovat tuto složku.</span><span class="sxs-lookup"><span data-stu-id="3de09-311">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="3de09-312">Všechny odkazy i nadále fungovat (vzhledem k tomu patří mezi ně neměli název složky).</span><span class="sxs-lookup"><span data-stu-id="3de09-312">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3de09-313">Přesměrování na stránku jinou [oblasti](xref:mvc/controllers/areas), určete oblast:</span><span class="sxs-lookup"><span data-stu-id="3de09-313">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="3de09-314">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="3de09-314">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="3de09-315">Atribut viewData</span><span class="sxs-lookup"><span data-stu-id="3de09-315">ViewData attribute</span></span>

<span data-ttu-id="3de09-316">Data mohou být předána na stránku s [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="3de09-316">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="3de09-317">Upravené vlastnosti v kontrolerech a modelech stránky Razor pomocí `[ViewData]` hodnoty uložené a načíst z [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="3de09-317">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="3de09-318">V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost upravené pomocí `[ViewData]`.</span><span class="sxs-lookup"><span data-stu-id="3de09-318">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="3de09-319">`Title` Je nastavena na název stránky o:</span><span class="sxs-lookup"><span data-stu-id="3de09-319">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="3de09-320">Na stránce o přístup `Title` vlastnost jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="3de09-320">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="3de09-321">Název je v rozložení pro čtení ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="3de09-321">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="3de09-322">TempData</span><span class="sxs-lookup"><span data-stu-id="3de09-322">TempData</span></span>

<span data-ttu-id="3de09-323">Zpřístupňuje ASP.NET Core [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) vlastnosti [řadič](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="3de09-323">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="3de09-324">Tato vlastnost ukládá data, dokud je pro čtení.</span><span class="sxs-lookup"><span data-stu-id="3de09-324">This property stores data until it's read.</span></span> <span data-ttu-id="3de09-325">`Keep` a `Peek` metody můžete použít k prozkoumání dat bez potřeby jejich odstranění.</span><span class="sxs-lookup"><span data-stu-id="3de09-325">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="3de09-326">`TempData` je užitečné pro přesměrování, když je potřeba data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="3de09-326">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="3de09-327">`[TempData]` Atribut je nového v ASP.NET Core 2.0 a je podporován v řadiče a stránky.</span><span class="sxs-lookup"><span data-stu-id="3de09-327">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="3de09-328">Následující kód nastaví hodnotu `Message` pomocí `TempData`:</span><span class="sxs-lookup"><span data-stu-id="3de09-328">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="3de09-329">Následující kód v *Pages/Customers/Index.cshtml* souboru zobrazí hodnotu `Message` pomocí `TempData`.</span><span class="sxs-lookup"><span data-stu-id="3de09-329">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="3de09-330">*Pages/Customers/Index.cshtml.cs* modelu stránka se vztahuje `[TempData]` atribut `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3de09-330">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="3de09-331">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="3de09-331">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="3de09-332">Na stránce více obslužných rutin</span><span class="sxs-lookup"><span data-stu-id="3de09-332">Multiple handlers per page</span></span>

<span data-ttu-id="3de09-333">Na následující stránce generuje kód pro dvě stránky obslužné rutiny `asp-page-handler` pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="3de09-333">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="3de09-334">Formuláře v předchozím příkladu má dva odeslání tlačítka, každý použití `FormActionTagHelper` pro odeslání do jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3de09-334">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="3de09-335">`asp-page-handler` Atribut, který je k `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="3de09-335">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="3de09-336">`asp-page-handler` generuje adresy URL, které odesílají na jednotlivé metody obslužné rutiny, které jsou definované na stránce.</span><span class="sxs-lookup"><span data-stu-id="3de09-336">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="3de09-337">`asp-page` není zadaný, protože vzorku je odkazování na aktuální stránce.</span><span class="sxs-lookup"><span data-stu-id="3de09-337">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="3de09-338">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="3de09-338">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="3de09-339">Předchozí kód používá *s názvem metody obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="3de09-339">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="3de09-340">Metody s názvem obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="3de09-340">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="3de09-341">V předchozím příkladu jsou metody stránky OnPost**JoinList**Async a OnPost**JoinListUC**asynchronní.</span><span class="sxs-lookup"><span data-stu-id="3de09-341">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="3de09-342">S *OnPost* a *asynchronní* odebrat, jsou názvy obslužných rutin `JoinList` a `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="3de09-342">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="3de09-343">Použití předcházející kódu, cesty URL, která odešle `OnPostJoinListAsync` je `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="3de09-343">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="3de09-344">Cesta URL, která odešle `OnPostJoinListUCAsync` je `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="3de09-344">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="3de09-345">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="3de09-345">Custom routes</span></span>

<span data-ttu-id="3de09-346">Použití `@page` direktivu do:</span><span class="sxs-lookup"><span data-stu-id="3de09-346">Use the `@page` directive to:</span></span>

* <span data-ttu-id="3de09-347">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="3de09-347">Specify a custom route to a page.</span></span> <span data-ttu-id="3de09-348">Například lze nastavit trasy, která má na stránce o `/Some/Other/Path` s `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="3de09-348">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="3de09-349">Připojte segmenty na stránce výchozí trasu.</span><span class="sxs-lookup"><span data-stu-id="3de09-349">Append segments to a page's default route.</span></span> <span data-ttu-id="3de09-350">Například můžete přidat segmentu "item" výchozí trasu na stránce s `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="3de09-350">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="3de09-351">Na stránce výchozí trasu připojení parametrů.</span><span class="sxs-lookup"><span data-stu-id="3de09-351">Append parameters to a page's default route.</span></span> <span data-ttu-id="3de09-352">Například pomocí parametru ID `id`, může být třeba stránka s `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="3de09-352">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="3de09-353">Relativní kořenové cesty určené tildou (`~`) se podporuje na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="3de09-353">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="3de09-354">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="3de09-354">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="3de09-355">Můžete změnit řetězec dotazu `?handler=JoinList` v adrese URL trasy segmentu `/JoinList` zadáním šablonu trasy `@page "{handler?}"`.</span><span class="sxs-lookup"><span data-stu-id="3de09-355">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="3de09-356">Pokud vám nevyhovují řetězec dotazu `?handler=JoinList` v adrese URL, můžete změnit trasy, které chcete změnit název obslužné rutiny na část cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="3de09-356">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="3de09-357">Trasy můžete přizpůsobit tak, že přidáte šablonu trasy, který je umístěn do dvojitých uvozovek po `@page` směrnice.</span><span class="sxs-lookup"><span data-stu-id="3de09-357">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="3de09-358">Použití předcházející kódu, cesty URL, která odešle `OnPostJoinListAsync` je `http://localhost:5000/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="3de09-358">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="3de09-359">Cesta URL, která odešle `OnPostJoinListUCAsync` je `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="3de09-359">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="3de09-360">`?` Následující `handler` znamená, že je parametr trasy nepovinný.</span><span class="sxs-lookup"><span data-stu-id="3de09-360">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="3de09-361">Konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="3de09-361">Configuration and settings</span></span>

<span data-ttu-id="3de09-362">Rozšířené možnosti nakonfigurujete, použijte metodu rozšíření `AddRazorPagesOptions` na tvůrce MVC:</span><span class="sxs-lookup"><span data-stu-id="3de09-362">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="3de09-363">Nyní můžete použít `RazorPagesOptions` nastavit kořenový adresář pro stránky, nebo přidat aplikaci modelu konvence pro stránky.</span><span class="sxs-lookup"><span data-stu-id="3de09-363">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="3de09-364">Jsme budou moci další rozšíření takto v budoucnu.</span><span class="sxs-lookup"><span data-stu-id="3de09-364">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="3de09-365">Předkompilace zobrazení, najdete v článku [kompilace zobrazení Razor](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="3de09-365">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="3de09-366">[Stažení nebo zobrazení vzorového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="3de09-366">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="3de09-367">Zobrazit [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start), které navazuje na tento úvod.</span><span class="sxs-lookup"><span data-stu-id="3de09-367">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="3de09-368">Určete, že pro stránky Razor se v kořenovém adresáři obsahu</span><span class="sxs-lookup"><span data-stu-id="3de09-368">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="3de09-369">Ve výchozím nastavení, je integrován Razor Pages */stránky* adresáře.</span><span class="sxs-lookup"><span data-stu-id="3de09-369">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="3de09-370">Přidat [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) k [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) k určení, že vaše stránky Razor se v kořenovém adresáři obsahu ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:</span><span class="sxs-lookup"><span data-stu-id="3de09-370">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="3de09-371">Určit, že Razor Pages na vlastní kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="3de09-371">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="3de09-372">Přidat [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) k [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) k určení, že jsou Razor Pages na vlastní kořenovým adresářem v aplikaci (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="3de09-372">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="3de09-373">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3de09-373">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
