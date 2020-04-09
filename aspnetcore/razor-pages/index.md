---
title: Úvod do Žiletky stránky v ASP.NET Core
author: Rick-Anderson
description: Zjistěte, jak Razor Pages v ASP.NET Core usnadňuje a ztráží scénáře zaměřené na stránky než při používání MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667578"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="6a1c4-103">Úvod do Žiletky stránky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a1c4-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6a1c4-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="6a1c4-105">Razor Pages může usnadnit a zefektivnit scénáře zaměřené na stránky než použití ovladačů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="6a1c4-106">Pokud hledáte kurz, který používá model-View-Controller přístup, najdete [v tématu Začínáme s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="6a1c4-107">Tento dokument obsahuje úvod do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="6a1c4-108">Není to krok za krokem tutorial.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="6a1c4-109">Pokud vám některé oddíly přijdou příliš pokročilé, přečtěte si část [Začínáme s razor pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="6a1c4-110">Přehled ASP.NET core najdete v [článku Úvod do ASP.NET jádra](xref:index).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a1c4-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6a1c4-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a1c4-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a1c4-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a1c4-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a1c4-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a1c4-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="6a1c4-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="6a1c4-115">Vytvoření projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6a1c4-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a1c4-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a1c4-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6a1c4-117">Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) Pages.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a1c4-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a1c4-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6a1c4-119">Spusťte `dotnet new webapp` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a1c4-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="6a1c4-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6a1c4-121">Spusťte `dotnet new webapp` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="6a1c4-122">Otevřete vygenerovaný soubor *.csproj* z Visual Studia for Mac.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="6a1c4-123">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6a1c4-123">Razor Pages</span></span>

<span data-ttu-id="6a1c4-124">Razor Stránky je povolena v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="6a1c4-125">Vezměme si základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="6a1c4-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="6a1c4-126">Předchozí kód vypadá hodně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používané v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="6a1c4-127">To, čím se [`@page`](xref:mvc/views/razor#page) liší, je směrnice.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-127">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="6a1c4-128">`@page`vytvoří soubor do akce MVC – což znamená, že zpracovává požadavky přímo, aniž by procházel řadičem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="6a1c4-129">`@page`musí být první direktivou Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="6a1c4-130">`@page`ovlivňuje chování jiných [konstrukce Razor.](xref:mvc/views/razor)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="6a1c4-131">Názvy souborů Razor Pages mají příponu *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="6a1c4-132">Podobná stránka pomocí `PageModel` třídy je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="6a1c4-133">Soubor *Pages/Index2.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="6a1c4-134">*Pages/Index2.cshtml.cs* model stránky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="6a1c4-135">Podle konvence `PageModel` má soubor třídy stejný název jako soubor Razor Page s připojeným *souborem .cs.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="6a1c4-136">Například předchozí stránka Razor je *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="6a1c4-137">Soubor obsahující třídu `PageModel` se nazývá *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="6a1c4-138">Přidružení cest adres URL ke stránkám jsou určena umístěním stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="6a1c4-139">V následující tabulce je uvedena cesta Razor Page a odpovídající adresa URL:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="6a1c4-140">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="6a1c4-140">File name and path</span></span>               | <span data-ttu-id="6a1c4-141">odpovídající adresa URL</span><span class="sxs-lookup"><span data-stu-id="6a1c4-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6a1c4-142">*/Stránky/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="6a1c4-143">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="6a1c4-144">*/Stránky/Kontakt.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="6a1c4-145">*/Stránky/Obchod/Kontakt.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="6a1c4-146">*/Stránky/Obchod/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="6a1c4-147">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="6a1c4-148">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-148">Notes:</span></span>

* <span data-ttu-id="6a1c4-149">Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="6a1c4-150">`Index`je výchozí stránka, pokud adresa URL stránku neobsahuje.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="6a1c4-151">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="6a1c4-151">Write a basic form</span></span>

<span data-ttu-id="6a1c4-152">Razor Pages je navržen tak, aby běžné vzory používané s webovými prohlížeči snadno implementovat při vytváření aplikace.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="6a1c4-153">[Vazby modelu](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro)a HTML pomocníky *všechny pracovat s* vlastnostmi definovanými ve třídě Razor Page.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="6a1c4-154">Vezměme si stránku, která implementuje `Contact` základní "kontaktujte nás" formulář pro model:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="6a1c4-155">Pro ukázky v tomto `DbContext` dokumentu je inicializován v [souboru Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="6a1c4-156">Datový model:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="6a1c4-157">Kontext db:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="6a1c4-158">Soubor zobrazení *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="6a1c4-159">Model stránky *Pages/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="6a1c4-160">Podle konvence `PageModel` je třída `<PageName>Model` volána a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="6a1c4-161">Třída `PageModel` umožňuje oddělení logiky stránky od jeho prezentace.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="6a1c4-162">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="6a1c4-163">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-163">This separation allows:</span></span>

* <span data-ttu-id="6a1c4-164">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="6a1c4-165">Testování jednotek</span><span class="sxs-lookup"><span data-stu-id="6a1c4-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="6a1c4-166">Stránka má `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` požadavky (když uživatel zaúčtuje formulář).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="6a1c4-167">Lze přidat metody obslužné rutiny pro libovolné sloveso HTTP.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="6a1c4-168">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-168">The most common handlers are:</span></span>

* <span data-ttu-id="6a1c4-169">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="6a1c4-170">V předchozím kódu `OnGet` metoda zobrazí *CreateModel.cshtml* Razor Page.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="6a1c4-171">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="6a1c4-172">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="6a1c4-173">Předchozí kód je typický pro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="6a1c4-174">Pokud jste obeznámeni s ASP.NET aplikacemi pomocí ovladačů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="6a1c4-175">Kód `OnPostAsync` v předchozím příkladu vypadá podobně jako typický kód řadiče.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="6a1c4-176">Většina primitiv MVC, jako je [vazba modelu](xref:mvc/models/model-binding), [ověření](xref:mvc/models/validation)a výsledky akce, funguje stejně s řadiči a razor pages.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="6a1c4-177">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="6a1c4-178">Základní tok `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="6a1c4-179">Zkontrolujte chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-179">Check for validation errors.</span></span>

* <span data-ttu-id="6a1c4-180">Pokud nejsou žádné chyby, uložte data a přesměrovat.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="6a1c4-181">Pokud dojde k chybám, zobrazte stránku znovu se zprávami ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="6a1c4-182">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="6a1c4-183">Soubor zobrazení *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="6a1c4-184">Vykreslený HTML ze *stránek/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="6a1c4-185">V předchozím kódu zaúčtování formuláře:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="6a1c4-186">S platnými údaji:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-186">With valid data:</span></span>

  * <span data-ttu-id="6a1c4-187">Metoda `OnPostAsync` obslužné rutiny volá pomocnou <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> metodu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="6a1c4-188">`RedirectToPage` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="6a1c4-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="6a1c4-190">Je výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-190">Is an action result.</span></span>
    * <span data-ttu-id="6a1c4-191">Je podobný `RedirectToAction` `RedirectToRoute` nebo (používá se v řadičích a zobrazeních).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="6a1c4-192">Je přizpůsoben pro stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-192">Is customized for pages.</span></span> <span data-ttu-id="6a1c4-193">V předchozím vzorku přesměruje na kořenovou stránku indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="6a1c4-194">`RedirectToPage`je podrobně popsána v části [Generování adres URL pro stránky.](#url_gen)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="6a1c4-195">S chybami ověření, které jsou předány serveru:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="6a1c4-196">Metoda `OnPostAsync` obslužné rutiny volá pomocnou <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> metodu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="6a1c4-197">`Page` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="6a1c4-198">Vrácení `Page` je podobné jako jak se `View`vrátí akce v řadičích .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="6a1c4-199">`PageResult`je výchozí návratový typ metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="6a1c4-200">Metoda obslužné rutiny, která vrátí `void` vykreslí stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="6a1c4-201">V předchozím příkladu zaúčtování formuláře bez hodnoty výsledky [v ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) vrácení false.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="6a1c4-202">V této ukázce nejsou na straně klienta zobrazeny žádné chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="6a1c4-203">Předání chyby ověření je popsáno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="6a1c4-204">S chybami ověření zjištěnými ověřením na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="6a1c4-205">Data **nejsou** zaúčtována na server.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="6a1c4-206">Ověření na straně klienta je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="6a1c4-207">Vlastnost `Customer` používá [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atribut k přihlášení k vazby modelu:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="6a1c4-208">`[BindProperty]`by **neměl** být používán na modely obsahující vlastnosti, které by neměly být změněny klientem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="6a1c4-209">Další informace naleznete v tématu [Overpost](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="6a1c4-210">Razor Pages ve výchozím nastavení vázají vlastnosti pouze s neslovesy.`GET`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="6a1c4-211">Vazba na vlastnosti odebere potřebu psaní kódu pro převod dat HTTP na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="6a1c4-212">Vazba snižuje kód pomocí stejné vlastnosti`<input asp-for="Customer.Name">`k vykreslení polí formuláře ( ) a přijetí vstupu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="6a1c4-213">Kontrola souboru zobrazení *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="6a1c4-214">V předchozím kódu sváže [pomocný](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` prvek vstupní `<input>` značky `Customer.Name` element HTML s výrazem modelu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="6a1c4-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)zpřístupní pomocné spoje značek.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="6a1c4-216">Domovská stránka</span><span class="sxs-lookup"><span data-stu-id="6a1c4-216">The home page</span></span>

<span data-ttu-id="6a1c4-217">*Index.cshtml* je domovská stránka:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="6a1c4-218">Přidružená `PageModel` třída (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="6a1c4-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6a1c4-219">Soubor *Index.cshtml* obsahuje následující značky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="6a1c4-220">Pomocník `<a /a>` [značky kotvy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použil `asp-route-{value}` atribut ke generování odkazu na stránku Úpravy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="6a1c4-221">Spojení obsahuje data trasy s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="6a1c4-222">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="6a1c4-223">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="6a1c4-224">Soubor *Index.cshtml* obsahuje značky pro vytvoření tlačítka pro odstranění pro každý kontakt zákazníka:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="6a1c4-225">Vykreslený KÓD HTML:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="6a1c4-226">Když je tlačítko delete vykresleno v HTML, jeho [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) obsahuje parametry pro:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="6a1c4-227">ID kontaktu zákazníka určené `asp-route-id` atributem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="6a1c4-228">. `handler`určené atributem. `asp-page-handler`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="6a1c4-229">Je-li tlačítko vybráno, je na server odeslán požadavek na formulář. `POST`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="6a1c4-230">Podle konvence je název metody obslužné rutiny vybrán na základě hodnoty parametru `handler` podle schématu `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="6a1c4-231">Vzhledem `handler` `delete` k tomu, `OnPostDeleteAsync` že je v tomto `POST` příkladu, metoda obslužné rutiny se používá ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="6a1c4-232">Pokud `asp-page-handler` je nastavena na jinou `remove`hodnotu, například `OnPostRemoveAsync` , je vybrána metoda obslužné rutiny s názvem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="6a1c4-233">Metoda: `OnPostDeleteAsync`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="6a1c4-234">Získá `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="6a1c4-235">Dotazuje databázi pro `FindAsync`kontakt zákazníka s .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="6a1c4-236">Pokud je kontakt zákazníka nalezen, je odebrán a databáze je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="6a1c4-237">Volání <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> k přesměrování na kořenovou`/Index`stránku indexu ( ).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="6a1c4-238">Soubor Edit.cshtml</span><span class="sxs-lookup"><span data-stu-id="6a1c4-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="6a1c4-239">První řádek obsahuje `@page "{id:int}"` směrnice.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="6a1c4-240">Omezení`"{id:int}"` směrování informuje stránku o přijetí požadavků `int` na stránku, která obsahuje data trasy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="6a1c4-241">Pokud požadavek na stránku neobsahuje data trasy, která `int`lze převést na , vrátí za běhu chyba HTTP 404 (nebyl nalezen).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6a1c4-242">Chcete-li, aby id `?` volitelné, připojit k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6a1c4-243">Soubor *Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="6a1c4-244">Ověřování</span><span class="sxs-lookup"><span data-stu-id="6a1c4-244">Validation</span></span>

<span data-ttu-id="6a1c4-245">Ověřovací pravidla:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-245">Validation rules:</span></span>

* <span data-ttu-id="6a1c4-246">Jsou deklarativně zadány ve třídě modelu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="6a1c4-247">Jsou vynuceny všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="6a1c4-248">Obor <xref:System.ComponentModel.DataAnnotations> názvů poskytuje sadu předdefinovaných ověřovacích atributů, které jsou deklarativně použity na třídu nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="6a1c4-249">DataAnnotations také obsahuje atributy [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) formátování, jako je to, které pomáhají s formátováním a neposkytují žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="6a1c4-250">Vezměme `Customer` si model:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="6a1c4-251">Pomocí následujícího souboru zobrazení *Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="6a1c4-252">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-252">The preceding code:</span></span>

* <span data-ttu-id="6a1c4-253">Zahrnuje skripty pro ověření jQuery a jQuery.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="6a1c4-254">Pomocí `<div />` `<span />` [pomocníků a značek](xref:mvc/views/tag-helpers/intro) povolte:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="6a1c4-255">Ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-255">Client-side validation.</span></span>
  * <span data-ttu-id="6a1c4-256">Vykreslování chyb ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-256">Validation error rendering.</span></span>

* <span data-ttu-id="6a1c4-257">Generuje následující HTML:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="6a1c4-258">Zaúčtováním formuláře Vytvořit bez hodnoty názvu se zobrazí chybová zpráva "Pole Název je povinné".</span><span class="sxs-lookup"><span data-stu-id="6a1c4-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="6a1c4-259">na formuláři.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-259">on the form.</span></span> <span data-ttu-id="6a1c4-260">Pokud je v klientovi povolen JavaScript, prohlížeč zobrazí chybu bez odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="6a1c4-261">Atribut `[StringLength(10)]` generuje `data-val-length-max="10"` na vykreslenéhtml.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="6a1c4-262">`data-val-length-max`zabrání prohlížečům v zadávání více, než je zadaná maximální délka.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="6a1c4-263">Pokud nástroj, jako je [Šumař](https://www.telerik.com/fiddler) se používá k úpravě a přehrání post:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="6a1c4-264">S názvem delším než 10.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-264">With the name longer than 10.</span></span>
* <span data-ttu-id="6a1c4-265">Chybová zpráva "Název pole musí být řetězec s maximální délkou 10."</span><span class="sxs-lookup"><span data-stu-id="6a1c4-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="6a1c4-266">je vrácena.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-266">is returned.</span></span>

<span data-ttu-id="6a1c4-267">Zvažte `Movie` následující model:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="6a1c4-268">Atributy ověření určují chování, které se má vynutit u vlastností modelu, na které se aplikují:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="6a1c4-269">`Required` Atributy `MinimumLength` a označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa, aby bylo možné vyhovět tomuto ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="6a1c4-270">Atribut `RegularExpression` se používá k omezení, jaké znaky lze zadat.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="6a1c4-271">V předchozím kódu "Žánr":</span><span class="sxs-lookup"><span data-stu-id="6a1c4-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="6a1c4-272">Musí používat pouze písmena.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-272">Must only use letters.</span></span>
  * <span data-ttu-id="6a1c4-273">První písmeno musí být velkými písmeny.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="6a1c4-274">Prázdné znaky, čísla a speciální znaky nejsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="6a1c4-275">"Hodnocení": `RegularExpression`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="6a1c4-276">Vyžaduje, aby první znak byl velké písmeno.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="6a1c4-277">Umožňuje speciální znaky a čísla v následujících mezerách.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="6a1c4-278">"PG-13" je platný pro hodnocení, ale selže pro "Žánr".</span><span class="sxs-lookup"><span data-stu-id="6a1c4-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="6a1c4-279">Atribut `Range` omezuje hodnotu v konkrétním rozsahu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="6a1c4-280">Atribut `StringLength` nastaví maximální délku vlastnosti řetězce a volitelně jeho minimální délku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="6a1c4-281">Typy `decimal`hodnot (například `float` `DateTime`, `int`, ) jsou ze své `[Required]` podstaty povinné a nepotřebují atribut.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="6a1c4-282">Na stránce Vytvořit `Movie` pro model se zobrazí chyby s neplatnými hodnotami:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulář zobrazení filmu s více chybami ověření na straně klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="6a1c4-284">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-284">For more information, see:</span></span>

* [<span data-ttu-id="6a1c4-285">Přidání ověření do aplikace Film</span><span class="sxs-lookup"><span data-stu-id="6a1c4-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="6a1c4-286">[Ověření modelu v ASP.NET jádra](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="6a1c4-287">Zpracování požadavků HEAD s záložní obslužnou rutinou OnGet</span><span class="sxs-lookup"><span data-stu-id="6a1c4-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="6a1c4-288">`HEAD`požadavky umožňují načítání záhlaví pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="6a1c4-289">Na `GET` rozdíl `HEAD` od požadavků požadavky nevracejí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="6a1c4-290">Obvykle je `OnHead` vytvořena obslužná rutina a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="6a1c4-291">Razor Pages přejde `OnGet` zpět na `OnHead` volání obslužné rutiny, pokud není definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="6a1c4-292">XSRF/CSRF a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="6a1c4-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="6a1c4-293">Razor Stránky jsou [chráněny antiforgery validace](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="6a1c4-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny antiforgery do prvků formuláře HTML.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="6a1c4-295">Použití rozložení, částečných částek, šablon a pomocných spoje značek se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="6a1c4-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="6a1c4-296">Stránky pracují se všemi možnostmi modulu Razor View.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="6a1c4-297">Rozložení, částečné součásti, šablony, tag helpery, *_ViewStart.cshtml*a *_ViewImports.cshtml* pracovat stejným způsobem, jako to dělají pro konvenční zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="6a1c4-298">Pojďme declutter této stránce s využitím některých z těchto schopností.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="6a1c4-299">Přidání [stránky rozložení](xref:mvc/views/layout) do *stránky/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="6a1c4-300">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="6a1c4-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="6a1c4-301">Určuje rozložení každé stránky (pokud se stránka neodhlásí z rozložení).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="6a1c4-302">Importuje struktury HTML, jako je JavaScript a styly.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="6a1c4-303">Obsah razor stránky jsou vykresleny, kde `@RenderBody()` je volána.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="6a1c4-304">Další informace naleznete na [stránce rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="6a1c4-305">Vlastnost [Layout](xref:mvc/views/layout#specifying-a-layout) je nastavena v *pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="6a1c4-306">Rozložení je ve složce *Stránky/Sdílené.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="6a1c4-307">Stránky vyhledává jiná zobrazení (rozložení, šablony, částečné části) hierarchicky, počínaje stejnou složkou jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="6a1c4-308">Rozložení ve složce *Stránky/Sdílené* lze použít z libovolné stránky Razor ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="6a1c4-309">Soubor rozložení by měl být ve složce *Stránky/Sdílené.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="6a1c4-310">Doporučujeme **not** neumisužuje soubor rozložení do složky *Zobrazení nebo Sdílené.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="6a1c4-311">*Zobrazení/Sdílené* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="6a1c4-312">Razor Stránky jsou určeny k spoléhání se na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="6a1c4-313">Zobrazení hledání ze stránky Razor obsahuje složku *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="6a1c4-314">Rozložení, šablony a částečné použití s mvc řadiče a konvenční razor zobrazení *prostě funguje*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="6a1c4-315">Přidejte soubor *Pages/_ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="6a1c4-316">`@namespace`je vysvětleno dále v tutoriálu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="6a1c4-317">Směrnice `@addTagHelper` přináší [integrované pomocné názvy značek](xref:mvc/views/tag-helpers/builtin-th/Index) na všechny stránky ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="6a1c4-318">Směrnice `@namespace` nastavená na stránce:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="6a1c4-319">Směrnice `@namespace` nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="6a1c4-320">Směrnice `@model` nemusí obsahovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="6a1c4-321">Pokud `@namespace` je směrnice obsažena v *souboru _ViewImports.cshtml*, zadaný obor názvů poskytuje předponu `@namespace` pro generovaný obor názvů na stránce, která importuje direktivu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="6a1c4-322">Zbytek generovaného oboru názvů (přípona) je relativní cesta oddělená tečkami mezi složkou obsahující *_ViewImports.cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="6a1c4-323">Například `PageModel` třída *Pages/Customers/Edit.cshtml.cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="6a1c4-324">Soubor *Pages/_ViewImports.cshtml* nastavuje následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="6a1c4-325">Generovaný obor názvů pro *stránku Pages/Customers/Edit.cshtml* `PageModel` Razor Page je stejný jako třída.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="6a1c4-326">`@namespace`*také pracuje s konvenčními pohledy na Břitva.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="6a1c4-327">Zvažte soubor zobrazení *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="6a1c4-328">Aktualizovaný soubor *zobrazení Pages/Create.cshtml* s *souborem _ViewImports.cshtml* a předchozím souborem rozložení:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="6a1c4-329">V předchozím kódu *_ViewImports.cshtml* importoval obor názvů a pomocné názvy značek.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="6a1c4-330">Soubor rozvržení importoval soubory JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="6a1c4-331">[Počáteční projekt Razor Pages](#rpvs17) obsahuje *pages/_ValidationScriptsPartial.cshtml*, který zavěsí ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="6a1c4-332">Další informace o částečných <xref:mvc/views/partial>zobrazeních naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="6a1c4-333">Generování adres URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="6a1c4-333">URL generation for Pages</span></span>

<span data-ttu-id="6a1c4-334">Stránka, zobrazená `Create` dříve, používá `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="6a1c4-335">Aplikace má následující strukturu souborů a složek:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="6a1c4-336">*/Stránky*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-336">*/Pages*</span></span>

  * <span data-ttu-id="6a1c4-337">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="6a1c4-338">*Ochrana osobních údajů.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="6a1c4-339">*/Zákazníci*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-339">*/Customers*</span></span>

    * <span data-ttu-id="6a1c4-340">*Vytvořit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="6a1c4-341">*Upravit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="6a1c4-342">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-342">*Index.cshtml*</span></span>

<span data-ttu-id="6a1c4-343">Stránky *Stránky/Zákazníci/Create.cshtml* a *Stránky/Zákazníci/Edit.cshtml* se po úspěchu přesměrují na *Stránky/Zákazníky/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="6a1c4-344">Řetězec `./Index` je relativní název stránky používaný pro přístup k předchozí stránce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="6a1c4-345">Používá se ke generování adres URL na stránce *Stránky/Zákazníci/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="6a1c4-346">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="6a1c4-347">Absolutní název `/Index` stránky se používá ke generování adres URL na stránce *Pages/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="6a1c4-348">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="6a1c4-349">Název stránky je cesta ke stránce ze složky root `/` */Pages* `/Index`včetně prokladu (například).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="6a1c4-350">Předchozí ukázky generování adres URL nabízejí rozšířené možnosti a funkční funkce oproti pevnému kódování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="6a1c4-351">Generování adresy URL používá [směrování](xref:mvc/controllers/routing) a může generovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="6a1c4-352">Generování adres URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="6a1c4-353">V následující tabulce je uvedeno, `RedirectToPage` která stránka Rejstřík je vybrána pomocí různých parametrů v *části Stránky/Zákazníci/Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="6a1c4-354">PřesměrovatToPage(x)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-354">RedirectToPage(x)</span></span>| <span data-ttu-id="6a1c4-355">stránka</span><span class="sxs-lookup"><span data-stu-id="6a1c4-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6a1c4-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="6a1c4-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="6a1c4-357">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-357">*Pages/Index*</span></span> |
| <span data-ttu-id="6a1c4-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="6a1c4-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="6a1c4-359">*Stránky/Zákazníci/Index*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="6a1c4-360">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="6a1c4-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="6a1c4-361">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-361">*Pages/Index*</span></span> |
| <span data-ttu-id="6a1c4-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="6a1c4-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="6a1c4-363">*Stránky/Zákazníci/Index*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="6a1c4-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="6a1c4-365">Parametr `RedirectToPage` je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="6a1c4-366">Relativní název propojení je užitečné při vytváření lokalit se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="6a1c4-367">Při použití relativních názvů k propojení stránek ve složce:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="6a1c4-368">Přejmenování složky nepřeruší relativní odkazy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="6a1c4-369">Odkazy nejsou přerušeny, protože neobsahují název složky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="6a1c4-370">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="6a1c4-371">Další informace naleznete v tématech <xref:mvc/controllers/areas> a <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="6a1c4-372">Atribut ViewData</span><span class="sxs-lookup"><span data-stu-id="6a1c4-372">ViewData attribute</span></span>

<span data-ttu-id="6a1c4-373">Data mohou být předána <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>na stránku s .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="6a1c4-374">Vlastnosti `[ViewData]` s atributem mají své <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>hodnoty uložené a načtené z .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="6a1c4-375">V následujícím příkladu `AboutModel` použije `[ViewData]` atribut vlastnosti: `Title`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="6a1c4-376">Na stránce O aplikaci přistupujte k vlastnosti `Title` jako k vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="6a1c4-377">V rozložení se název čte ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="6a1c4-378">TempData</span><span class="sxs-lookup"><span data-stu-id="6a1c4-378">TempData</span></span>

<span data-ttu-id="6a1c4-379">ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="6a1c4-380">Tato vlastnost ukládá data, dokud se číst.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-380">This property stores data until it's read.</span></span> <span data-ttu-id="6a1c4-381">Metody <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> a lze použít ke kontrole dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="6a1c4-382">`TempData`je užitečné pro přesměrování, když jsou data potřebná pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="6a1c4-383">Následující kód nastaví `Message` hodnotu použití `TempData`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="6a1c4-384">Následující značky v souboru *Pages/Customers/Index.cshtml* `Message` zobrazují `TempData`hodnotu použití .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="6a1c4-385">Model stránky *Pages/Customers/Index.cshtml.cs* použije `[TempData]` atribut `Message` na vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="6a1c4-386">Další informace naleznete v tématu [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="6a1c4-387">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="6a1c4-387">Multiple handlers per page</span></span>

<span data-ttu-id="6a1c4-388">Následující stránka generuje značky pro dvě `asp-page-handler` obslužné rutiny pomocí pomocníka pro označení:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="6a1c4-389">Formulář v předchozím příkladu má dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každé používá k odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="6a1c4-390">Atribut `asp-page-handler` je společníkem `asp-page`aplikace .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="6a1c4-391">`asp-page-handler`generuje adresy URL, které se odesílají ke každé metodě obslužné rutiny definované stránkou.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="6a1c4-392">`asp-page`není zadán, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="6a1c4-393">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="6a1c4-394">Předchozí kód používá *metody pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="6a1c4-395">Pojmenované metody obslužné rutiny `On<HTTP Verb>` jsou `Async` vytvořeny tak, že text v názvu za a před (pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="6a1c4-396">V předchozím příkladu jsou metody stránky OnPost**JoinList**Async a OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="6a1c4-397">Při odebrání *onpost* a *async* `JoinList` jsou `JoinListUC`názvy obslužných rutin a .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="6a1c4-398">Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList`cesta url, která se odešle, .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="6a1c4-399">Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`se odešle, je .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="6a1c4-400">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="6a1c4-400">Custom routes</span></span>

<span data-ttu-id="6a1c4-401">Pomocí `@page` směrnice můžete:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="6a1c4-402">Zadejte vlastní trasu ke stránce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-402">Specify a custom route to a page.</span></span> <span data-ttu-id="6a1c4-403">Například trasu na stránku Informace `/Some/Other/Path` lze `@page "/Some/Other/Path"`nastavit pomocí .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="6a1c4-404">Připojujte segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-404">Append segments to a page's default route.</span></span> <span data-ttu-id="6a1c4-405">Segment "položka" lze například přidat k výchozí trase `@page "item"`stránky s .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="6a1c4-406">Připojujte parametry k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="6a1c4-407">Například parametr ID `id`, může být vyžadován `@page "{id}"`pro stránku s .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="6a1c4-408">Je podporována cesta relativní ke`~`kořenu určená vlnovkou ( ) na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="6a1c4-409">Například `@page "~/Some/Other/Path"` je stejný `@page "/Some/Other/Path"`jako .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="6a1c4-410">Pokud se vám nelíbí řetězec `?handler=JoinList` dotazu v adrese URL, změňte trasu tak, aby název obslužné rutiny v části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="6a1c4-411">Trasu lze přizpůsobit přidáním šablony trasy uzavřené v `@page` uvozovkách za direktivu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="6a1c4-412">Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList`cesta url, která se odešle, .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="6a1c4-413">Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH/JoinListUC`se odešle, je .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="6a1c4-414">Následující `?` `handler` znamená, že parametr trasy je volitelný.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="6a1c4-415">Pokročilá konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="6a1c4-415">Advanced configuration and settings</span></span>

<span data-ttu-id="6a1c4-416">Konfigurace a nastavení v následujících částech není vyžadováno většinou aplikací.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="6a1c4-417">Chcete-li konfigurovat rozšířené možnosti, použijte metodu <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>rozšíření :</span><span class="sxs-lookup"><span data-stu-id="6a1c4-417">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="6a1c4-418">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> nastavení kořenového adresáře pro stránky nebo k přidání konvencí aplikačního modelu pro stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="6a1c4-419">Další informace o konvencích naleznete v tématu [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="6a1c4-420">Chcete-li předkompilovat zobrazení, viz [kompilace zobrazení razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="6a1c4-421">Určete, že Razor Pages jsou v kořenovém adresáři obsahu</span><span class="sxs-lookup"><span data-stu-id="6a1c4-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="6a1c4-422">Ve výchozím nastavení jsou Razor Pages zakořeněny v adresáři */Pages.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="6a1c4-423">Přidejte <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> a určete, že vaše stránky Razor jsou v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) aplikace:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="6a1c4-424">Určete, že Razor Pages jsou ve vlastním kořenovém adresáři</span><span class="sxs-lookup"><span data-stu-id="6a1c4-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="6a1c4-425">Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> určit, že Razor Stránky jsou na vlastní kořenový adresář v aplikaci (poskytnout relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="6a1c4-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="6a1c4-426">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6a1c4-426">Additional resources</span></span>

* <span data-ttu-id="6a1c4-427">Viz [Začínáme se stránkami Razor ,](xref:tutorials/razor-pages/razor-pages-start)které vycházejí z tohoto úvodu</span><span class="sxs-lookup"><span data-stu-id="6a1c4-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="6a1c4-428">Stažení nebo zobrazení ukázkového kódu</span><span class="sxs-lookup"><span data-stu-id="6a1c4-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6a1c4-429">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="6a1c4-430">Razor Pages je nový aspekt ASP.NET Core MVC, který usnadňuje a zprostředkovává kódování scénářů zaměřených na stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-430">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="6a1c4-431">Pokud hledáte kurz, který používá model-View-Controller přístup, najdete [v tématu Začínáme s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="6a1c4-432">Tento dokument obsahuje úvod do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="6a1c4-433">Není to krok za krokem tutorial.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="6a1c4-434">Pokud vám některé oddíly přijdou příliš pokročilé, přečtěte si část [Začínáme s razor pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="6a1c4-435">Přehled ASP.NET core najdete v [článku Úvod do ASP.NET jádra](xref:index).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a1c4-436">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6a1c4-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a1c4-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a1c4-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a1c4-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a1c4-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a1c4-439">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="6a1c4-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="6a1c4-440">Vytvoření projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6a1c4-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a1c4-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a1c4-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6a1c4-442">Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme se stránkami Razor](xref:tutorials/razor-pages/razor-pages-start) Pages.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a1c4-443">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="6a1c4-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6a1c4-444">Spusťte `dotnet new webapp` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="6a1c4-445">Otevřete vygenerovaný soubor *.csproj* z Visual Studia for Mac.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a1c4-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a1c4-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6a1c4-447">Spusťte `dotnet new webapp` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="6a1c4-448">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6a1c4-448">Razor Pages</span></span>

<span data-ttu-id="6a1c4-449">Razor Stránky je povolena v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-449">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="6a1c4-450">Vezměme si základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="6a1c4-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="6a1c4-451">Předchozí kód vypadá hodně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používané v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="6a1c4-452">To, čím se `@page` liší, je směrnice.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="6a1c4-453">`@page`vytvoří soubor do akce MVC – což znamená, že zpracovává požadavky přímo, aniž by procházel řadičem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="6a1c4-454">`@page`musí být první direktivou Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="6a1c4-455">`@page`ovlivňuje chování jiných konstrukce Razor.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="6a1c4-456">Podobná stránka pomocí `PageModel` třídy je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="6a1c4-457">Soubor *Pages/Index2.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="6a1c4-458">*Pages/Index2.cshtml.cs* model stránky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="6a1c4-459">Podle konvence `PageModel` má soubor třídy stejný název jako soubor Razor Page s připojeným *souborem .cs.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="6a1c4-460">Například předchozí stránka Razor je *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="6a1c4-461">Soubor obsahující třídu `PageModel` se nazývá *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="6a1c4-462">Přidružení cest adres URL ke stránkám jsou určena umístěním stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="6a1c4-463">V následující tabulce je uvedena cesta Razor Page a odpovídající adresa URL:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="6a1c4-464">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="6a1c4-464">File name and path</span></span>               | <span data-ttu-id="6a1c4-465">odpovídající adresa URL</span><span class="sxs-lookup"><span data-stu-id="6a1c4-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6a1c4-466">*/Stránky/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="6a1c4-467">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="6a1c4-468">*/Stránky/Kontakt.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="6a1c4-469">*/Stránky/Obchod/Kontakt.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="6a1c4-470">*/Stránky/Obchod/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="6a1c4-471">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="6a1c4-472">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-472">Notes:</span></span>

* <span data-ttu-id="6a1c4-473">Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="6a1c4-474">`Index`je výchozí stránka, pokud adresa URL stránku neobsahuje.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="6a1c4-475">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="6a1c4-475">Write a basic form</span></span>

<span data-ttu-id="6a1c4-476">Razor Pages je navržen tak, aby běžné vzory používané s webovými prohlížeči snadno implementovat při vytváření aplikace.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-476">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="6a1c4-477">[Vazby modelu](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro)a HTML pomocníky *všechny pracovat s* vlastnostmi definovanými ve třídě Razor Page.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="6a1c4-478">Vezměme si stránku, která implementuje `Contact` základní "kontaktujte nás" formulář pro model:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="6a1c4-479">Pro ukázky v tomto `DbContext` dokumentu je inicializován v [souboru Startup.cs.](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="6a1c4-480">Datový model:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="6a1c4-481">Kontext db:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="6a1c4-482">Soubor zobrazení *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="6a1c4-483">Model stránky *Pages/Create.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="6a1c4-484">Podle konvence `PageModel` je třída `<PageName>Model` volána a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="6a1c4-485">Třída `PageModel` umožňuje oddělení logiky stránky od jeho prezentace.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="6a1c4-486">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="6a1c4-487">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-487">This separation allows:</span></span>

* <span data-ttu-id="6a1c4-488">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="6a1c4-489">[Jednotka testuje](xref:test/razor-pages-tests) stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="6a1c4-490">Stránka má `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` požadavky (když uživatel zaúčtuje formulář).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="6a1c4-491">Můžete přidat metody obslužné rutiny pro jakýkoli příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="6a1c4-492">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-492">The most common handlers are:</span></span>

* <span data-ttu-id="6a1c4-493">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="6a1c4-494">[OnGet](#OnGet) ukázku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="6a1c4-495">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="6a1c4-496">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="6a1c4-497">Předchozí kód je typický pro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="6a1c4-498">Pokud jste obeznámeni s ASP.NET aplikacemi pomocí ovladačů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="6a1c4-499">Kód `OnPostAsync` v předchozím příkladu vypadá podobně jako typický kód řadiče.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="6a1c4-500">Většina primitiv MVC, jako je [vazba modelu](xref:mvc/models/model-binding), [ověření](xref:mvc/models/validation), [ověření](xref:mvc/models/validation)a výsledky akce jsou sdíleny.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="6a1c4-501">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="6a1c4-502">Základní tok `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="6a1c4-503">Zkontrolujte chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-503">Check for validation errors.</span></span>

* <span data-ttu-id="6a1c4-504">Pokud nejsou žádné chyby, uložte data a přesměrovat.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="6a1c4-505">Pokud dojde k chybám, zobrazte stránku znovu se zprávami ověření.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="6a1c4-506">Ověření na straně klienta je shodné s tradičními aplikacemi ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="6a1c4-507">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="6a1c4-508">Když jsou data úspěšně zadána, `OnPostAsync` metoda `RedirectToPage` obslužné rutiny zavolá pomocnou metodu k vrácení instance `RedirectToPageResult`aplikace .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="6a1c4-509">`RedirectToPage`je nový výsledek akce, `RedirectToAction` `RedirectToRoute`podobný nebo , ale přizpůsobené pro stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="6a1c4-510">V předchozím vzorku přesměruje na kořenovou stránku indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="6a1c4-511">`RedirectToPage`je podrobně popsána v části [Generování adres URL pro stránky.](#url_gen)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="6a1c4-512">Pokud odeslaný formulář má chyby ověření (které`OnPostAsync` jsou předány serveru), metoda obslužné rutiny volá pomocnou metodu. `Page`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="6a1c4-513">`Page` vrací instanci `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="6a1c4-514">Vrácení `Page` je podobné jako jak se `View`vrátí akce v řadičích .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="6a1c4-515">`PageResult`je výchozí návratový typ metody obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="6a1c4-516">Metoda obslužné rutiny, která vrátí `void` vykreslí stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="6a1c4-517">Vlastnost `Customer` používá `[BindProperty]` atribut vyjádřit se k přihlášení k vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="6a1c4-518">Razor Pages ve výchozím nastavení vázají vlastnosti pouze s neslovesy.`GET`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-518">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="6a1c4-519">Vazba na vlastnosti může snížit množství kódu, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="6a1c4-520">Vazba snižuje kód pomocí stejné vlastnosti`<input asp-for="Customer.Name">`k vykreslení polí formuláře ( ) a přijetí vstupu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="6a1c4-521">Domovská stránka (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="6a1c4-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="6a1c4-522">Přidružená `PageModel` třída (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="6a1c4-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="6a1c4-523">Soubor *Index.cshtml* obsahuje následující značky pro vytvoření odkazu pro úpravy pro každý kontakt:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="6a1c4-524">Pomocník `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [značky kotvy](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použil `asp-route-{value}` atribut ke generování odkazu na stránku Úpravy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="6a1c4-525">Spojení obsahuje data trasy s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="6a1c4-526">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="6a1c4-527">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="6a1c4-528">Pomocné spoje tagů jsou povoleny`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="6a1c4-529">Soubor *Stránky/Edit.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="6a1c4-530">První řádek obsahuje `@page "{id:int}"` směrnice.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="6a1c4-531">Omezení`"{id:int}"` směrování informuje stránku o přijetí požadavků `int` na stránku, která obsahuje data trasy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="6a1c4-532">Pokud požadavek na stránku neobsahuje data trasy, která `int`lze převést na , vrátí za běhu chyba HTTP 404 (nebyl nalezen).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="6a1c4-533">Chcete-li, aby id `?` volitelné, připojit k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="6a1c4-534">Soubor *Pages/Edit.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="6a1c4-535">Soubor *Index.cshtml* také obsahuje značky pro vytvoření tlačítka pro odstranění pro každý kontakt zákazníka:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="6a1c4-536">Když je tlačítko odstranit vykresleno `formaction` v HTML, obsahuje jeho parametry pro:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="6a1c4-537">ID kontaktu zákazníka `asp-route-id` určené atributem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="6a1c4-538">Určené `handler` atributem. `asp-page-handler`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="6a1c4-539">Zde je příklad vykresleného tlačítka odstranění s ID `1`kontaktu zákazníka :</span><span class="sxs-lookup"><span data-stu-id="6a1c4-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="6a1c4-540">Je-li tlačítko vybráno, je na server odeslán požadavek na formulář. `POST`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="6a1c4-541">Podle konvence je název metody obslužné rutiny vybrán na základě hodnoty parametru `handler` podle schématu `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="6a1c4-542">Vzhledem `handler` `delete` k tomu, `OnPostDeleteAsync` že je v tomto `POST` příkladu, metoda obslužné rutiny se používá ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="6a1c4-543">Pokud `asp-page-handler` je nastavena na jinou `remove`hodnotu, například `OnPostRemoveAsync` , je vybrána metoda obslužné rutiny s názvem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="6a1c4-544">Následující kód ukazuje `OnPostDeleteAsync` obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="6a1c4-545">Metoda: `OnPostDeleteAsync`</span><span class="sxs-lookup"><span data-stu-id="6a1c4-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="6a1c4-546">Přijme `id` řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="6a1c4-547">Pokud *index.cshtml* směrnice stránky obsahovalomezení směrování `"{id:int?}"`, `id` by pocházet z dat trasy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="6a1c4-548">Data trasy `id` pro je určena v `https://localhost:5001/Customers/2`identifikátoru URI, například .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="6a1c4-549">Dotazuje databázi pro `FindAsync`kontakt zákazníka s .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="6a1c4-550">Pokud je kontakt zákazníka nalezen, bude odebrán ze seznamu kontaktů zákazníků.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="6a1c4-551">Databáze je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-551">The database is updated.</span></span>
* <span data-ttu-id="6a1c4-552">Volání `RedirectToPage` k přesměrování na kořenovou`/Index`stránku indexu ( ).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="6a1c4-553">Označit vlastnosti stránky podle potřeby</span><span class="sxs-lookup"><span data-stu-id="6a1c4-553">Mark page properties as required</span></span>

<span data-ttu-id="6a1c4-554">Vlastnosti `PageModel` na a lze označit [atributem Povinné:](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="6a1c4-555">Další informace naleznete v [tématu Ověření modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="6a1c4-556">Zpracování požadavků HEAD s záložní obslužnou rutinou OnGet</span><span class="sxs-lookup"><span data-stu-id="6a1c4-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="6a1c4-557">`HEAD`požadavky umožňují načíst záhlaví pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="6a1c4-558">Na `GET` rozdíl `HEAD` od požadavků požadavky nevracejí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="6a1c4-559">Obvykle je `OnHead` vytvořena obslužná rutina a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="6a1c4-560">V ASP.NET Jádrem 2.1 nebo novějším, `OnGet` Razor `OnHead` Pages přejde zpět na volání obslužné rutiny, pokud není definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="6a1c4-561">Toto chování je povoleno volání [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="6a1c4-562">Výchozí šablony generovat `SetCompatibilityVersion` volání v ASP.NET Core 2.1 a 2.2.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="6a1c4-563">`SetCompatibilityVersion`efektivně nastaví volbu `AllowMappingHeadRequestsToGetHandler` `true`Razor Pages na .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="6a1c4-564">Místo toho, abyste se přihlásili ke všem chováním pomocí `SetCompatibilityVersion`, můžete se explicitně přihlásit ke *konkrétnímu* chování.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="6a1c4-565">Následující kód se přihlásí k `HEAD` povolení požadavků, které `OnGet` mají být mapovány na obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="6a1c4-566">XSRF/CSRF a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="6a1c4-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="6a1c4-567">Nemusíte psát žádný kód pro [ověření antiforgery](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="6a1c4-568">Generování a validace tokenů antiforgery jsou automaticky zahrnuty do stránek Razor.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="6a1c4-569">Použití rozložení, částečných částek, šablon a pomocných spoje značek se stránkami Razor</span><span class="sxs-lookup"><span data-stu-id="6a1c4-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="6a1c4-570">Stránky pracují se všemi možnostmi modulu Razor View.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="6a1c4-571">Rozložení, částečné, šablony, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* fungují stejným způsobem jako pro konvenční zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="6a1c4-572">Pojďme declutter této stránce s využitím některých z těchto schopností.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="6a1c4-573">Přidání [stránky rozložení](xref:mvc/views/layout) do *stránky/Shared/_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="6a1c4-574">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="6a1c4-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="6a1c4-575">Určuje rozložení každé stránky (pokud se stránka neodhlásí z rozložení).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="6a1c4-576">Importuje struktury HTML, jako je JavaScript a styly.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="6a1c4-577">Další informace najdete na [stránce rozložení.](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="6a1c4-578">Vlastnost [Layout](xref:mvc/views/layout#specifying-a-layout) je nastavena v *pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="6a1c4-579">Rozložení je ve složce *Stránky/Sdílené.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="6a1c4-580">Stránky vyhledává jiná zobrazení (rozložení, šablony, částečné části) hierarchicky, počínaje stejnou složkou jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="6a1c4-581">Rozložení ve složce *Stránky/Sdílené* lze použít z libovolné stránky Razor ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="6a1c4-582">Soubor rozložení by měl být ve složce *Stránky/Sdílené.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="6a1c4-583">Doporučujeme **not** neumisužuje soubor rozložení do složky *Zobrazení nebo Sdílené.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="6a1c4-584">*Zobrazení/Sdílené* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-584">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="6a1c4-585">Razor Stránky jsou určeny k spoléhání se na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-585">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="6a1c4-586">Zobrazení hledání ze stránky Razor obsahuje složku *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="6a1c4-587">Rozložení, šablony a částečné části, které používáte s řadiči MVC a konvenční zobrazení Razor *prostě funguje*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="6a1c4-588">Přidejte soubor *Pages/_ViewImports.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="6a1c4-589">`@namespace`je vysvětleno dále v tutoriálu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="6a1c4-590">Směrnice `@addTagHelper` přináší [integrované pomocné názvy značek](xref:mvc/views/tag-helpers/builtin-th/Index) na všechny stránky ve složce *Stránky.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="6a1c4-591">Při `@namespace` použití směrnice explicitně na stránce:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="6a1c4-592">Směrnice nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="6a1c4-593">Směrnice `@model` nemusí obsahovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="6a1c4-594">Pokud `@namespace` je směrnice obsažena v *souboru _ViewImports.cshtml*, zadaný obor názvů poskytuje předponu `@namespace` pro generovaný obor názvů na stránce, která importuje direktivu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="6a1c4-595">Zbytek generovaného oboru názvů (přípona) je relativní cesta oddělená tečkami mezi složkou obsahující *_ViewImports.cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="6a1c4-596">Například `PageModel` třída *Pages/Customers/Edit.cshtml.cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="6a1c4-597">Soubor *Pages/_ViewImports.cshtml* nastavuje následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="6a1c4-598">Generovaný obor názvů pro *stránku Pages/Customers/Edit.cshtml* `PageModel` Razor Page je stejný jako třída.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="6a1c4-599">`@namespace`*také pracuje s konvenčními pohledy na Břitva.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="6a1c4-600">Původní soubor zobrazení *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="6a1c4-601">Aktualizovaný soubor zobrazení *Pages/Create.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="6a1c4-602">[Počáteční projekt Razor Pages](#rpvs17) obsahuje *pages/_ValidationScriptsPartial.cshtml*, který zavěsí ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="6a1c4-603">Další informace o částečných <xref:mvc/views/partial>zobrazeních naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="6a1c4-604">Generování adres URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="6a1c4-604">URL generation for Pages</span></span>

<span data-ttu-id="6a1c4-605">Stránka, zobrazená `Create` dříve, používá `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="6a1c4-606">Aplikace má následující strukturu souborů a složek:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="6a1c4-607">*/Stránky*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-607">*/Pages*</span></span>

  * <span data-ttu-id="6a1c4-608">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="6a1c4-609">*/Zákazníci*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-609">*/Customers*</span></span>

    * <span data-ttu-id="6a1c4-610">*Vytvořit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="6a1c4-611">*Upravit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="6a1c4-612">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-612">*Index.cshtml*</span></span>

<span data-ttu-id="6a1c4-613">Stránky *Stránky/Zákazníci/Create.cshtml* a *Stránky/Zákazníci/Edit.cshtml* se po úspěchu přesměrují na *Stránky/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="6a1c4-614">Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="6a1c4-615">Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *Pages/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="6a1c4-616">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="6a1c4-617">Název stránky je cesta ke stránce ze složky root `/` */Pages* `/Index`včetně prokladu (například).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="6a1c4-618">Předchozí ukázky generování adres URL nabízejí rozšířené možnosti a funkční funkce oproti pevně kódování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="6a1c4-619">Generování adresy URL používá [směrování](xref:mvc/controllers/routing) a může generovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="6a1c4-620">Generování adres URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="6a1c4-621">V následující tabulce je uvedeno, `RedirectToPage` která stránka Rejstřík je vybrána s různými parametry ze *stránek/zákazníků/create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="6a1c4-622">PřesměrovatToPage(x)</span><span class="sxs-lookup"><span data-stu-id="6a1c4-622">RedirectToPage(x)</span></span>| <span data-ttu-id="6a1c4-623">stránka</span><span class="sxs-lookup"><span data-stu-id="6a1c4-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6a1c4-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="6a1c4-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="6a1c4-625">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-625">*Pages/Index*</span></span> |
| <span data-ttu-id="6a1c4-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="6a1c4-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="6a1c4-627">*Stránky/Zákazníci/Index*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="6a1c4-628">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="6a1c4-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="6a1c4-629">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-629">*Pages/Index*</span></span> |
| <span data-ttu-id="6a1c4-630">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="6a1c4-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="6a1c4-631">*Stránky/Zákazníci/Index*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="6a1c4-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="6a1c4-633">Parametr `RedirectToPage` je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="6a1c4-634">Relativní název propojení je užitečné při vytváření lokalit se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="6a1c4-635">Pokud k propojení stránek ve složce používáte relativní názvy, můžete tuto složku přejmenovat.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="6a1c4-636">Všechny odkazy stále fungují (protože neobsahovaly název složky).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="6a1c4-637">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="6a1c4-638">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="6a1c4-639">Atribut ViewData</span><span class="sxs-lookup"><span data-stu-id="6a1c4-639">ViewData attribute</span></span>

<span data-ttu-id="6a1c4-640">Data mohou být předána na stránku pomocí [atributu ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="6a1c4-641">Vlastnosti na řadičích nebo `[ViewData]` modelech Razor Page s atributem mají své hodnoty uložené a načtené z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="6a1c4-642">V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost `[ViewData]`označenou .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="6a1c4-643">Vlastnost `Title` je nastavena na název stránky Informace:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="6a1c4-644">Na stránce O aplikaci přistupujte k vlastnosti `Title` jako k vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="6a1c4-645">V rozložení se název čte ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="6a1c4-646">TempData</span><span class="sxs-lookup"><span data-stu-id="6a1c4-646">TempData</span></span>

<span data-ttu-id="6a1c4-647">ASP.NET Core zpřístupňuje [Vlastnost TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="6a1c4-648">Tato vlastnost ukládá data, dokud se číst.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-648">This property stores data until it's read.</span></span> <span data-ttu-id="6a1c4-649">Metody `Keep` `Peek` a lze použít ke kontrole dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="6a1c4-650">`TempData`je užitečné pro přesměrování, když jsou data potřebná pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="6a1c4-651">Následující kód nastaví `Message` hodnotu použití `TempData`:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="6a1c4-652">Následující značky v souboru *Pages/Customers/Index.cshtml* `Message` zobrazují `TempData`hodnotu použití .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="6a1c4-653">Model stránky *Pages/Customers/Index.cshtml.cs* použije `[TempData]` atribut `Message` na vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="6a1c4-654">Další informace naleznete v tématu [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="6a1c4-655">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="6a1c4-655">Multiple handlers per page</span></span>

<span data-ttu-id="6a1c4-656">Následující stránka generuje značky pro dvě `asp-page-handler` obslužné rutiny pomocí pomocníka pro označení:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="6a1c4-657">Formulář v předchozím příkladu má dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každé používá k odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="6a1c4-658">Atribut `asp-page-handler` je společníkem `asp-page`aplikace .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="6a1c4-659">`asp-page-handler`generuje adresy URL, které se odesílají ke každé metodě obslužné rutiny definované stránkou.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="6a1c4-660">`asp-page`není zadán, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="6a1c4-661">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="6a1c4-662">Předchozí kód používá *metody pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="6a1c4-663">Pojmenované metody obslužné rutiny `On<HTTP Verb>` jsou `Async` vytvořeny tak, že text v názvu za a před (pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="6a1c4-664">V předchozím příkladu jsou metody stránky OnPost**JoinList**Async a OnPost**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="6a1c4-665">Při odebrání *onpost* a *async* `JoinList` jsou `JoinListUC`názvy obslužných rutin a .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="6a1c4-666">Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList`cesta url, která se odešle, .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="6a1c4-667">Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`se odešle, je .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="6a1c4-668">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="6a1c4-668">Custom routes</span></span>

<span data-ttu-id="6a1c4-669">Pomocí `@page` směrnice můžete:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="6a1c4-670">Zadejte vlastní trasu ke stránce.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-670">Specify a custom route to a page.</span></span> <span data-ttu-id="6a1c4-671">Například trasu na stránku Informace `/Some/Other/Path` lze `@page "/Some/Other/Path"`nastavit pomocí .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="6a1c4-672">Připojujte segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-672">Append segments to a page's default route.</span></span> <span data-ttu-id="6a1c4-673">Segment "položka" lze například přidat k výchozí trase `@page "item"`stránky s .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="6a1c4-674">Připojujte parametry k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="6a1c4-675">Například parametr ID `id`, může být vyžadován `@page "{id}"`pro stránku s .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="6a1c4-676">Je podporována cesta relativní ke`~`kořenu určená vlnovkou ( ) na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="6a1c4-677">Například `@page "~/Some/Other/Path"` je stejný `@page "/Some/Other/Path"`jako .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="6a1c4-678">Pokud se vám nelíbí řetězec `?handler=JoinList` dotazu v adrese URL, změňte trasu tak, aby název obslužné rutiny v části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="6a1c4-679">Trasu lze přizpůsobit přidáním šablony trasy uzavřené v `@page` uvozovkách za direktivu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="6a1c4-680">Pomocí předchozího kódu je `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList`cesta url, která se odešle, .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="6a1c4-681">Cesta url, do `OnPostJoinListUCAsync` které `https://localhost:5001/Customers/CreateFATH/JoinListUC`se odešle, je .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="6a1c4-682">Následující `?` `handler` znamená, že parametr trasy je volitelný.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="6a1c4-683">Konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="6a1c4-683">Configuration and settings</span></span>

<span data-ttu-id="6a1c4-684">Chcete-li konfigurovat rozšířené možnosti, použijte metodu `AddRazorPagesOptions` rozšíření na tvůrce MVC:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="6a1c4-685">V současné době `RazorPagesOptions` můžete použít k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="6a1c4-686">V budoucnu umožníme větší roztažnost tímto způsobem.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="6a1c4-687">Chcete-li předkompilovat zobrazení, viz [kompilace zobrazení razor](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="6a1c4-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="6a1c4-688">[Stáhnout nebo zobrazit ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="6a1c4-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="6a1c4-689">Viz [Začínáme se stránkami Razor ,](xref:tutorials/razor-pages/razor-pages-start)které vychází z tohoto úvodu.</span><span class="sxs-lookup"><span data-stu-id="6a1c4-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="6a1c4-690">Určete, že Razor Pages jsou v kořenovém adresáři obsahu</span><span class="sxs-lookup"><span data-stu-id="6a1c4-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="6a1c4-691">Ve výchozím nastavení jsou Razor Pages zakořeněny v adresáři */Pages.*</span><span class="sxs-lookup"><span data-stu-id="6a1c4-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="6a1c4-692">Přidejte [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, že vaše stránky Razor jsou v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) [(ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:</span><span class="sxs-lookup"><span data-stu-id="6a1c4-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="6a1c4-693">Určete, že Razor Pages jsou ve vlastním kořenovém adresáři</span><span class="sxs-lookup"><span data-stu-id="6a1c4-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="6a1c4-694">Přidejte [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, že vaše Razor Pages jsou ve vlastním kořenovém adresáři v aplikaci (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="6a1c4-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="6a1c4-695">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6a1c4-695">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
