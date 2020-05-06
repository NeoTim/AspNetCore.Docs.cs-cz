---
title: Úvod do Razor stránek v ASP.NET Core
author: Rick-Anderson
description: Přečtěte Razor si, jak stránky v ASP.NET Core zjednodušují a produktivnější vytváření kódu na stránce, než pomocí MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 827a0df4c914fd012c55e6612a987713bfbaa5c2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777212"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="3e433-103">Úvod do Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e433-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e433-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="3e433-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="3e433-105">Razor Pages může vytvářet kódovací scénáře zaměřené na stránky a lépe a produktivnější než používání řadičů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3e433-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="3e433-106">Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="3e433-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="3e433-107">Tento dokument představuje úvod do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3e433-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="3e433-108">Nejedná se o podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="3e433-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="3e433-109">Pokud některé části obsahují příliš pokročilé, přečtěte si téma Začínáme [s Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="3e433-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="3e433-110">Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="3e433-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e433-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3e433-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e433-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e433-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e433-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e433-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e433-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e433-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="3e433-115">Vytvoření projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e433-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e433-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3e433-117">Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="3e433-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e433-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e433-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3e433-119">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3e433-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e433-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e433-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3e433-121">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3e433-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="3e433-122">Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="3e433-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="3e433-123">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-123">Razor Pages</span></span>

<span data-ttu-id="3e433-124">Razor Pages je povolený v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3e433-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="3e433-125">Zvažte základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="3e433-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="3e433-126">Předchozí kód vypadá podobně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="3e433-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="3e433-127">To znamená, že tato [`@page`](xref:mvc/views/razor#page) direktiva je odlišná.</span><span class="sxs-lookup"><span data-stu-id="3e433-127">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="3e433-128">`@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler.</span><span class="sxs-lookup"><span data-stu-id="3e433-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="3e433-129">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="3e433-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="3e433-130">`@page`má vliv na chování jiných konstrukcí [Razor](xref:mvc/views/razor) .</span><span class="sxs-lookup"><span data-stu-id="3e433-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="3e433-131">Názvy souborů Razor Pages mají příponu *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3e433-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="3e433-132">Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="3e433-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="3e433-133">Soubor *Pages/Index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="3e433-134">Model stránky *stránky/Index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="3e433-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="3e433-135">Podle konvence má `PageModel` soubor třídy stejný název jako soubor stránky Razor s *příponou. cs* .</span><span class="sxs-lookup"><span data-stu-id="3e433-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="3e433-136">Například předchozí stránka Razor je *Pages/Index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e433-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="3e433-137">Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="3e433-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="3e433-138">Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="3e433-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="3e433-139">Následující tabulka ukazuje cestu stránky Razor a adresu URL pro porovnání:</span><span class="sxs-lookup"><span data-stu-id="3e433-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="3e433-140">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="3e433-140">File name and path</span></span>               | <span data-ttu-id="3e433-141">shodná adresa URL</span><span class="sxs-lookup"><span data-stu-id="3e433-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3e433-142">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="3e433-143">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="3e433-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="3e433-144">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="3e433-145">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="3e433-146">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="3e433-147">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="3e433-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="3e433-148">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="3e433-148">Notes:</span></span>

* <span data-ttu-id="3e433-149">Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="3e433-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="3e433-150">`Index`je výchozí stránka, když adresa URL neobsahuje stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="3e433-151">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="3e433-151">Write a basic form</span></span>

<span data-ttu-id="3e433-152">Razor Pages je navržený tak, aby se při vytváření aplikace usnadnily běžné vzory používané s webovými prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="3e433-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="3e433-153">[Vázání modelů](xref:mvc/models/model-binding), [pomocníkům značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníkům, kteří *pracují pouze* s vlastnostmi definovanými ve třídě stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="3e433-154">Zvažte stránku, která pro `Contact` model implementuje základní formulář "kontaktujte nás":</span><span class="sxs-lookup"><span data-stu-id="3e433-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="3e433-155">Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .</span><span class="sxs-lookup"><span data-stu-id="3e433-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="3e433-156">Datový model:</span><span class="sxs-lookup"><span data-stu-id="3e433-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="3e433-157">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="3e433-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="3e433-158">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="3e433-159">Model stránky *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="3e433-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="3e433-160">Podle konvence je `PageModel` třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="3e433-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="3e433-161">`PageModel` Třída umožňuje oddělení logiky stránky od její prezentace.</span><span class="sxs-lookup"><span data-stu-id="3e433-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="3e433-162">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="3e433-163">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="3e433-163">This separation allows:</span></span>

* <span data-ttu-id="3e433-164">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3e433-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="3e433-165">Testování jednotek</span><span class="sxs-lookup"><span data-stu-id="3e433-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="3e433-166">Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle).</span><span class="sxs-lookup"><span data-stu-id="3e433-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="3e433-167">Lze přidat metody obslužné rutiny pro jakýkoli příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="3e433-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="3e433-168">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="3e433-168">The most common handlers are:</span></span>

* <span data-ttu-id="3e433-169">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="3e433-170">V předchozím kódu `OnGet` metoda zobrazí stránku *CreateModel. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="3e433-171">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="3e433-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="3e433-172">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="3e433-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="3e433-173">Předchozí kód je typický pro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3e433-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="3e433-174">Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3e433-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="3e433-175">`OnPostAsync` Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e433-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="3e433-176">Většina primitivních primitiv MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation)a výsledky akce, fungují stejně jako u řadičů a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3e433-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="3e433-177">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="3e433-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3e433-178">Základní tok `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="3e433-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="3e433-179">Kontrola chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="3e433-179">Check for validation errors.</span></span>

* <span data-ttu-id="3e433-180">V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.</span><span class="sxs-lookup"><span data-stu-id="3e433-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="3e433-181">Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami.</span><span class="sxs-lookup"><span data-stu-id="3e433-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="3e433-182">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="3e433-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="3e433-183">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="3e433-184">Vykreslený HTML ze *stránky/vytvořit. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3e433-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="3e433-185">V předchozím kódu vyúčtováním formuláře:</span><span class="sxs-lookup"><span data-stu-id="3e433-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="3e433-186">S platnými daty:</span><span class="sxs-lookup"><span data-stu-id="3e433-186">With valid data:</span></span>

  * <span data-ttu-id="3e433-187">Metoda `OnPostAsync` obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="3e433-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="3e433-188">`RedirectToPage` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="3e433-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="3e433-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="3e433-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="3e433-190">Je výsledkem akce.</span><span class="sxs-lookup"><span data-stu-id="3e433-190">Is an action result.</span></span>
    * <span data-ttu-id="3e433-191">Je podobný `RedirectToAction` nebo `RedirectToRoute` (používá se v řadičích a zobrazeních).</span><span class="sxs-lookup"><span data-stu-id="3e433-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="3e433-192">Je přizpůsoben pro stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-192">Is customized for pages.</span></span> <span data-ttu-id="3e433-193">V předchozí ukázce přesměruje na stránku kořenového indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="3e433-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="3e433-194">`RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="3e433-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="3e433-195">S chybami ověřování, které jsou předány serveru:</span><span class="sxs-lookup"><span data-stu-id="3e433-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="3e433-196">Metoda `OnPostAsync` obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="3e433-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="3e433-197">`Page` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="3e433-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="3e433-198">Vrácení `Page` se podobá tomu, jak vrátí `View`akce v řadičích.</span><span class="sxs-lookup"><span data-stu-id="3e433-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="3e433-199">`PageResult`je výchozí návratový typ pro metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3e433-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="3e433-200">Metoda obslužné rutiny, `void` která vrací vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="3e433-201">V předchozím příkladu publikování formuláře bez výsledků hodnoty v [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) vrací hodnotu false.</span><span class="sxs-lookup"><span data-stu-id="3e433-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="3e433-202">V této ukázce nejsou v klientovi zobrazeny žádné chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="3e433-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="3e433-203">V tomto dokumentu se pojednává o chybách ověřování.</span><span class="sxs-lookup"><span data-stu-id="3e433-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="3e433-204">S chybami ověřování zjištěnými ověřováním na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="3e433-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="3e433-205">Data **nejsou** odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="3e433-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="3e433-206">Ověřování na straně klienta je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="3e433-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="3e433-207">`Customer` Vlastnost používá [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atribut pro výslovný souhlas s vazbou modelu:</span><span class="sxs-lookup"><span data-stu-id="3e433-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="3e433-208">`[BindProperty]`neměl **by se** používat pro modely obsahující vlastnosti, které by klient neměl měnit.</span><span class="sxs-lookup"><span data-stu-id="3e433-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="3e433-209">Další informace najdete v tématu [přestavení](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="3e433-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="3e433-210">Razor Pages ve výchozím nastavení vlastnosti BIND pouze s jinými než`GET` slovesy.</span><span class="sxs-lookup"><span data-stu-id="3e433-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="3e433-211">Vazba na vlastnosti odstraní nutnost psaní kódu pro převod dat HTTP na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="3e433-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="3e433-212">Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře (`<input asp-for="Customer.Name">`) a přijměte vstup.</span><span class="sxs-lookup"><span data-stu-id="3e433-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="3e433-213">Prohlíží se soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="3e433-214">V předchozím kódu `<input asp-for="Customer.Name" />` [Pomocník vstupní značky](xref:mvc/views/working-with-forms#the-input-tag-helper) váže prvek HTML `<input>` na výraz `Customer.Name` modelu.</span><span class="sxs-lookup"><span data-stu-id="3e433-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="3e433-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)zpřístupňuje pomocníkům značky.</span><span class="sxs-lookup"><span data-stu-id="3e433-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="3e433-216">Domovská stránka</span><span class="sxs-lookup"><span data-stu-id="3e433-216">The home page</span></span>

<span data-ttu-id="3e433-217">*Index. cshtml* je Domovská stránka:</span><span class="sxs-lookup"><span data-stu-id="3e433-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="3e433-218">Přidružená `PageModel` třída (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3e433-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3e433-219">Soubor *index. cshtml* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="3e433-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="3e433-220">`<a /a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="3e433-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="3e433-221">Odkaz obsahuje data směrování s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="3e433-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="3e433-222">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="3e433-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="3e433-223">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="3e433-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="3e433-224">Soubor *index. cshtml* obsahuje značky pro vytvoření tlačítka Odstranit pro každého kontaktu zákazníka:</span><span class="sxs-lookup"><span data-stu-id="3e433-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="3e433-225">Vykreslený kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e433-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="3e433-226">Pokud je tlačítko Odstranit vykresleno ve formátu HTML, obsahuje [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametry pro:</span><span class="sxs-lookup"><span data-stu-id="3e433-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="3e433-227">ID kontaktu zákazníka určené `asp-route-id` atributem.</span><span class="sxs-lookup"><span data-stu-id="3e433-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="3e433-228">`handler`, Určené `asp-page-handler` atributem.</span><span class="sxs-lookup"><span data-stu-id="3e433-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="3e433-229">Když je vybráno tlačítko, pošle `POST` se na server požadavek na formulář.</span><span class="sxs-lookup"><span data-stu-id="3e433-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="3e433-230">Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu. `OnPost[handler]Async`</span><span class="sxs-lookup"><span data-stu-id="3e433-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="3e433-231">Vzhledem k `handler` tomu `delete` , že je v tomto `OnPostDeleteAsync` příkladu, metoda obslužné rutiny se `POST` používá ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="3e433-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="3e433-232">Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove`, je vybrána metoda obslužné rutiny s názvem. `OnPostRemoveAsync`</span><span class="sxs-lookup"><span data-stu-id="3e433-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="3e433-233">`OnPostDeleteAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="3e433-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="3e433-234">Získá `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="3e433-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="3e433-235">Dotazuje databázi na kontaktování zákazníka `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="3e433-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="3e433-236">Pokud se kontakt zákazníka najde, odebere se a aktualizuje se databáze.</span><span class="sxs-lookup"><span data-stu-id="3e433-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="3e433-237">Volání <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pro přesměrování na stránku kořenového indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="3e433-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="3e433-238">Upravit soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="3e433-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="3e433-239">První řádek obsahuje `@page "{id:int}"` direktivu.</span><span class="sxs-lookup"><span data-stu-id="3e433-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="3e433-240">Omezení`"{id:int}"` směrování instruuje stránku, aby přijímala požadavky na stránku, která `int` obsahuje data směrování.</span><span class="sxs-lookup"><span data-stu-id="3e433-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="3e433-241">Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int`, modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="3e433-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3e433-242">Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="3e433-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3e433-243">Soubor *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="3e433-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="3e433-244">Ověřování</span><span class="sxs-lookup"><span data-stu-id="3e433-244">Validation</span></span>

<span data-ttu-id="3e433-245">Ověřovací pravidla:</span><span class="sxs-lookup"><span data-stu-id="3e433-245">Validation rules:</span></span>

* <span data-ttu-id="3e433-246">Jsou deklarativně určeny ve třídě modelu.</span><span class="sxs-lookup"><span data-stu-id="3e433-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="3e433-247">Jsou vynutily všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3e433-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="3e433-248"><xref:System.ComponentModel.DataAnnotations> Obor názvů poskytuje sadu předdefinovaných ověřovacích atributů, které jsou aplikovány deklarativně na třídu nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3e433-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="3e433-249">Tato dataanotace také obsahuje atributy formátování [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) , jako jsou tyto informace užitečné při formátování a neposkytují žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="3e433-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="3e433-250">Vezměte v `Customer` úvahu model:</span><span class="sxs-lookup"><span data-stu-id="3e433-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="3e433-251">Pomocí následujícího souboru zobrazení *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="3e433-252">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="3e433-252">The preceding code:</span></span>

* <span data-ttu-id="3e433-253">Zahrnuje skripty pro ověření jQuery a jQuery.</span><span class="sxs-lookup"><span data-stu-id="3e433-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="3e433-254">Používá `<div />` `<span />` [pomocníky značek](xref:mvc/views/tag-helpers/intro) a k povolení:</span><span class="sxs-lookup"><span data-stu-id="3e433-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="3e433-255">Ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3e433-255">Client-side validation.</span></span>
  * <span data-ttu-id="3e433-256">Vykreslování chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="3e433-256">Validation error rendering.</span></span>

* <span data-ttu-id="3e433-257">Generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3e433-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="3e433-258">Při publikování formuláře vytvořit bez hodnoty Name se zobrazí chybová zpráva "pole název je povinné."</span><span class="sxs-lookup"><span data-stu-id="3e433-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="3e433-259">ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="3e433-259">on the form.</span></span> <span data-ttu-id="3e433-260">Pokud je v klientovi povolený jazyk JavaScript, zobrazí se v prohlížeči chyba bez odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="3e433-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="3e433-261">`[StringLength(10)]` Atribut generuje `data-val-length-max="10"` na vykresleném HTML.</span><span class="sxs-lookup"><span data-stu-id="3e433-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="3e433-262">`data-val-length-max`zabrání prohlížečům v zadávání více než maximální zadané délky.</span><span class="sxs-lookup"><span data-stu-id="3e433-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="3e433-263">Pokud se k úpravě a přehrání příspěvku používá nástroj, například [Fiddler](https://www.telerik.com/fiddler) :</span><span class="sxs-lookup"><span data-stu-id="3e433-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="3e433-264">S názvem delším než 10.</span><span class="sxs-lookup"><span data-stu-id="3e433-264">With the name longer than 10.</span></span>
* <span data-ttu-id="3e433-265">Chybová zpráva "název pole musí být řetězec s maximální délkou 10."</span><span class="sxs-lookup"><span data-stu-id="3e433-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="3e433-266">je vrácen.</span><span class="sxs-lookup"><span data-stu-id="3e433-266">is returned.</span></span>

<span data-ttu-id="3e433-267">Vezměte v úvahu `Movie` následující model:</span><span class="sxs-lookup"><span data-stu-id="3e433-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="3e433-268">Atributy ověřování určují chování, které se má vyhovět pro vlastnosti modelu, na které se aplikují:</span><span class="sxs-lookup"><span data-stu-id="3e433-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="3e433-269">Atributy `Required` a `MinimumLength` označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.</span><span class="sxs-lookup"><span data-stu-id="3e433-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="3e433-270">`RegularExpression` Atribut slouží k omezení znaků, které lze zadat.</span><span class="sxs-lookup"><span data-stu-id="3e433-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="3e433-271">V předchozím kódu "Žánr":</span><span class="sxs-lookup"><span data-stu-id="3e433-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="3e433-272">Je nutné použít pouze písmena.</span><span class="sxs-lookup"><span data-stu-id="3e433-272">Must only use letters.</span></span>
  * <span data-ttu-id="3e433-273">První písmeno musí být velkými písmeny.</span><span class="sxs-lookup"><span data-stu-id="3e433-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="3e433-274">Mezery, číslice a speciální znaky nejsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="3e433-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="3e433-275">`RegularExpression` Hodnocení:</span><span class="sxs-lookup"><span data-stu-id="3e433-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="3e433-276">Vyžaduje, aby byl první znak velkým písmenem.</span><span class="sxs-lookup"><span data-stu-id="3e433-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="3e433-277">Umožňuje speciální znaky a čísla v následujících mezerách.</span><span class="sxs-lookup"><span data-stu-id="3e433-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="3e433-278">"PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="3e433-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="3e433-279">Atribut `Range` omezuje hodnotu v konkrétním rozsahu.</span><span class="sxs-lookup"><span data-stu-id="3e433-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="3e433-280">`StringLength` Atribut nastaví maximální délku řetězcové vlastnosti a volitelně její minimální délku.</span><span class="sxs-lookup"><span data-stu-id="3e433-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="3e433-281">Typy hodnot `decimal`(například, `int`, `float`, `DateTime`) jsou podstatně požadovány a nepotřebují `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="3e433-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="3e433-282">Stránka vytvořit pro `Movie` model zobrazuje chyby s neplatnými hodnotami:</span><span class="sxs-lookup"><span data-stu-id="3e433-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="3e433-284">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="3e433-284">For more information, see:</span></span>

* [<span data-ttu-id="3e433-285">Přidání ověření do aplikace Movie</span><span class="sxs-lookup"><span data-stu-id="3e433-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="3e433-286">[Ověřování modelu v ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3e433-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="3e433-287">Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet</span><span class="sxs-lookup"><span data-stu-id="3e433-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="3e433-288">`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="3e433-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="3e433-289">Na rozdíl `GET` od požadavků `HEAD` požadavky nevrátí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3e433-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="3e433-290">Obvykle je `OnHead` obslužná rutina vytvořena a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="3e433-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="3e433-291">Razor Pages se vrátí k volání `OnGet` obslužné rutiny, `OnHead` Pokud není definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="3e433-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="3e433-292">XSRF/CSRF a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="3e433-293">Razor Pages jsou chráněny [ověřováním proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="3e433-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="3e433-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny proti padělání do prvků formuláře HTML.</span><span class="sxs-lookup"><span data-stu-id="3e433-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="3e433-295">Použití rozložení, částečných, šablon a značek pomocníků s Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="3e433-296">Stránky fungují se všemi možnostmi modulu zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="3e433-297">Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*a *_ViewImports. cshtml* fungují stejným způsobem jako v případě tradičních zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="3e433-298">Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="3e433-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="3e433-299">Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3e433-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="3e433-300">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="3e433-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="3e433-301">Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).</span><span class="sxs-lookup"><span data-stu-id="3e433-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="3e433-302">Importuje struktury HTML, jako jsou JavaScript a StyleSheet.</span><span class="sxs-lookup"><span data-stu-id="3e433-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="3e433-303">Obsah stránky Razor se vykreslí tam, kde `@RenderBody()` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="3e433-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="3e433-304">Další informace najdete v tématu [rozložení stránky](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="3e433-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="3e433-305">Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3e433-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="3e433-306">Rozložení se nachází na *stránkách nebo ve sdílené* složce.</span><span class="sxs-lookup"><span data-stu-id="3e433-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="3e433-307">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="3e433-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="3e433-308">Rozložení na *stránkách nebo ve sdílené* složce se dá použít na libovolné stránce Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="3e433-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="3e433-309">Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="3e433-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="3e433-310">Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="3e433-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="3e433-311">*Zobrazení/Shared* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="3e433-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="3e433-312">Razor Pages mají spoléhat na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="3e433-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="3e433-313">Zobrazení hledání ze stránky Razor obsahuje složku *stránky* .</span><span class="sxs-lookup"><span data-stu-id="3e433-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="3e433-314">Rozložení, šablony a částečné typy používané s řadiči MVC a konvenčními zobrazeními Razor *fungují pouze*.</span><span class="sxs-lookup"><span data-stu-id="3e433-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="3e433-315">Přidejte soubor *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="3e433-316">`@namespace`je vysvětleno dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="3e433-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="3e433-317">Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="3e433-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="3e433-318">`@namespace` Direktiva nastavená na stránce:</span><span class="sxs-lookup"><span data-stu-id="3e433-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="3e433-319">`@namespace` Direktiva nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="3e433-320">`@model` Direktiva nemusí zahrnovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="3e433-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="3e433-321">Pokud je `@namespace` direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu.</span><span class="sxs-lookup"><span data-stu-id="3e433-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="3e433-322">Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="3e433-323">Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="3e433-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="3e433-324">Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="3e433-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="3e433-325">Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` třída.</span><span class="sxs-lookup"><span data-stu-id="3e433-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="3e433-326">`@namespace`*funguje také se konvenčními zobrazeními Razor.*</span><span class="sxs-lookup"><span data-stu-id="3e433-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="3e433-327">Zvažte *stránku zobrazení stránky/vytvořit soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="3e433-328">Aktualizovaný soubor zobrazení *Pages/Create. cshtml* s *_ViewImports. cshtml* a předchozím souborem rozložení:</span><span class="sxs-lookup"><span data-stu-id="3e433-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="3e433-329">V předchozím kódu *_ViewImports. cshtml* importovala obor názvů a pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="3e433-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="3e433-330">Soubor rozložení importoval soubory JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="3e433-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="3e433-331">[Projekt Razor Pages Starter](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které zapojování ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3e433-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="3e433-332">Další informace o částečných zobrazeních naleznete <xref:mvc/views/partial>v tématu.</span><span class="sxs-lookup"><span data-stu-id="3e433-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="3e433-333">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="3e433-333">URL generation for Pages</span></span>

<span data-ttu-id="3e433-334">Stránka `Create` , která se zobrazuje dřív, `RedirectToPage`používá:</span><span class="sxs-lookup"><span data-stu-id="3e433-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="3e433-335">Aplikace má následující strukturu souborů nebo složek:</span><span class="sxs-lookup"><span data-stu-id="3e433-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="3e433-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="3e433-336">*/Pages*</span></span>

  * <span data-ttu-id="3e433-337">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="3e433-338">*Soukromí. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="3e433-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="3e433-339">*/Customers*</span></span>

    * <span data-ttu-id="3e433-340">*Vytvořit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="3e433-341">*Upravit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="3e433-342">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-342">*Index.cshtml*</span></span>

<span data-ttu-id="3e433-343">*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit. cshtml* Pages přesměruje na *Pages/Customers/index. cshtml* po úspěchu.</span><span class="sxs-lookup"><span data-stu-id="3e433-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="3e433-344">Řetězec `./Index` je relativní název stránky, který slouží k přístupu na předchozí stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="3e433-345">Slouží ke generování adres URL na stránce *Pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3e433-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="3e433-346">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3e433-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="3e433-347">Absolutní název `/Index` stránky se používá ke generování adres URL na stránce *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3e433-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="3e433-348">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3e433-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="3e433-349">Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index`).</span><span class="sxs-lookup"><span data-stu-id="3e433-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="3e433-350">Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkční funkce v rámci hardwarového kódování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="3e433-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="3e433-351">Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="3e433-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="3e433-352">Generování adresy URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="3e433-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="3e433-353">Následující tabulka ukazuje, která stránka index je vybrána pomocí různých `RedirectToPage` parametrů na *stránkách/zákaznících/vytvořit. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e433-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="3e433-354">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="3e433-354">RedirectToPage(x)</span></span>| <span data-ttu-id="3e433-355">stránka</span><span class="sxs-lookup"><span data-stu-id="3e433-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3e433-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="3e433-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="3e433-357">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-357">*Pages/Index*</span></span> |
| <span data-ttu-id="3e433-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="3e433-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="3e433-359">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="3e433-360">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="3e433-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="3e433-361">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-361">*Pages/Index*</span></span> |
| <span data-ttu-id="3e433-362">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="3e433-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="3e433-363">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="3e433-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="3e433-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="3e433-365">`RedirectToPage` Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="3e433-366">Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="3e433-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="3e433-367">Když se k propojení mezi stránkami ve složce použijí relativní názvy:</span><span class="sxs-lookup"><span data-stu-id="3e433-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="3e433-368">Přejmenování složky neruší relativní odkazy.</span><span class="sxs-lookup"><span data-stu-id="3e433-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="3e433-369">Odkazy nejsou přerušeny, protože neobsahují název složky.</span><span class="sxs-lookup"><span data-stu-id="3e433-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="3e433-370">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="3e433-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="3e433-371">Další informace naleznete v tématech <xref:mvc/controllers/areas> a <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="3e433-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="3e433-372">ViewData – atribut</span><span class="sxs-lookup"><span data-stu-id="3e433-372">ViewData attribute</span></span>

<span data-ttu-id="3e433-373">Data je možné předat stránce pomocí <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span><span class="sxs-lookup"><span data-stu-id="3e433-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="3e433-374">Vlastnosti s `[ViewData]` atributem mají své hodnoty uložené a načtené z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span><span class="sxs-lookup"><span data-stu-id="3e433-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="3e433-375">V následujícím příkladu `AboutModel` použije `[ViewData]` atribut na `Title` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="3e433-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="3e433-376">Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="3e433-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="3e433-377">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="3e433-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="3e433-378">TempData</span><span class="sxs-lookup"><span data-stu-id="3e433-378">TempData</span></span>

<span data-ttu-id="3e433-379">ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span><span class="sxs-lookup"><span data-stu-id="3e433-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="3e433-380">Tato vlastnost ukládá data do jejich čtení.</span><span class="sxs-lookup"><span data-stu-id="3e433-380">This property stores data until it's read.</span></span> <span data-ttu-id="3e433-381">Metody <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> a <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> lze použít k prohlédnutí dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="3e433-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="3e433-382">`TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e433-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="3e433-383">Následující kód nastaví hodnotu `Message` pomocí: `TempData`</span><span class="sxs-lookup"><span data-stu-id="3e433-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="3e433-384">Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData`.</span><span class="sxs-lookup"><span data-stu-id="3e433-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="3e433-385">Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3e433-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="3e433-386">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="3e433-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="3e433-387">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="3e433-387">Multiple handlers per page</span></span>

<span data-ttu-id="3e433-388">Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="3e433-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="3e433-389">Formulář v předchozím příkladu obsahuje dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každá používá pro odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3e433-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="3e433-390">`asp-page-handler` Atribut je doprovodný objekt k `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="3e433-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="3e433-391">`asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou.</span><span class="sxs-lookup"><span data-stu-id="3e433-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="3e433-392">`asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="3e433-393">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="3e433-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="3e433-394">Předchozí kód používá *pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="3e433-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="3e433-395">Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="3e433-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="3e433-396">V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="3e433-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="3e433-397">Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="3e433-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="3e433-398">Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` kterou se odesílá,. `https://localhost:5001/Customers/CreateFATH?handler=JoinList`</span><span class="sxs-lookup"><span data-stu-id="3e433-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="3e433-399">Cesta URL, na `OnPostJoinListUCAsync` kterou se `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`odesílá.</span><span class="sxs-lookup"><span data-stu-id="3e433-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="3e433-400">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="3e433-400">Custom routes</span></span>

<span data-ttu-id="3e433-401">Použijte tuto `@page` direktivu pro:</span><span class="sxs-lookup"><span data-stu-id="3e433-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="3e433-402">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-402">Specify a custom route to a page.</span></span> <span data-ttu-id="3e433-403">Například trasa na stránku o aplikaci `/Some/Other/Path` `@page "/Some/Other/Path"`může být nastavena na.</span><span class="sxs-lookup"><span data-stu-id="3e433-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="3e433-404">Připojit segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-404">Append segments to a page's default route.</span></span> <span data-ttu-id="3e433-405">Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="3e433-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="3e433-406">Připojení parametrů k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="3e433-407">Například parametr `id`ID může být požadován pro stránku s `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="3e433-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="3e433-408">Je podporována kořenová relativní cesta, která je určena`~`vlnovkou () na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="3e433-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="3e433-409">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="3e433-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="3e433-410">Pokud se v adrese URL nelíbí `?handler=JoinList` řetězec dotazu, změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="3e433-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="3e433-411">Trasa se dá přizpůsobit přidáním šablony trasy, která je `@page` uzavřená do dvojitých uvozovek za direktivou.</span><span class="sxs-lookup"><span data-stu-id="3e433-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="3e433-412">Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` kterou se odesílá,. `https://localhost:5001/Customers/CreateFATH/JoinList`</span><span class="sxs-lookup"><span data-stu-id="3e433-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="3e433-413">Cesta URL, na `OnPostJoinListUCAsync` kterou se `https://localhost:5001/Customers/CreateFATH/JoinListUC`odesílá.</span><span class="sxs-lookup"><span data-stu-id="3e433-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="3e433-414">`?` Následující `handler` údaj znamená, že parametr trasy je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="3e433-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="3e433-415">Pokročilá konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="3e433-415">Advanced configuration and settings</span></span>

<span data-ttu-id="3e433-416">Konfigurace a nastavení v následujících oddílech není pro většinu aplikací vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="3e433-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="3e433-417">Chcete-li konfigurovat pokročilé možnosti, použijte metodu <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>rozšíření:</span><span class="sxs-lookup"><span data-stu-id="3e433-417">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="3e433-418">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> můžete nastavit kořenový adresář pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="3e433-419">Další informace o konvencích najdete v tématu [Razor Pages autorizačních konvencí](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="3e433-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="3e433-420">Chcete-li předkompilovat zobrazení, přečtěte si téma [kompilace zobrazení Razor](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="3e433-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="3e433-421">Zadejte, že Razor Pages jsou v kořenu obsahu.</span><span class="sxs-lookup"><span data-stu-id="3e433-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="3e433-422">Ve výchozím nastavení se Razor Pages rootem v adresáři */Pages* .</span><span class="sxs-lookup"><span data-stu-id="3e433-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="3e433-423">Přidejte <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> a určete, že vaše Razor Pages jsou v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) aplikace:</span><span class="sxs-lookup"><span data-stu-id="3e433-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="3e433-424">Zadejte, že Razor Pages jsou ve vlastním kořenovém adresáři</span><span class="sxs-lookup"><span data-stu-id="3e433-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="3e433-425">Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> k určení, že Razor Pages jsou ve vlastním kořenovém adresáři v aplikaci (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="3e433-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="3e433-426">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="3e433-426">Additional resources</span></span>

* <span data-ttu-id="3e433-427">Další informace najdete v tématu [Začínáme s Razor Pages, který sestává](xref:tutorials/razor-pages/razor-pages-start)z tohoto úvodního sestavení.</span><span class="sxs-lookup"><span data-stu-id="3e433-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="3e433-428">Stáhnout nebo zobrazit ukázkový kód</span><span class="sxs-lookup"><span data-stu-id="3e433-428">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
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

<span data-ttu-id="3e433-429">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="3e433-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="3e433-430">Razor Pages je nový aspekt ASP.NET Core MVC, který usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-430">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="3e433-431">Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="3e433-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="3e433-432">Tento dokument představuje úvod do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3e433-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="3e433-433">Nejedná se o podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="3e433-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="3e433-434">Pokud některé části obsahují příliš pokročilé, přečtěte si téma Začínáme [s Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="3e433-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="3e433-435">Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="3e433-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e433-436">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3e433-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e433-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e433-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e433-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e433-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e433-439">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e433-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="3e433-440">Vytvoření projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e433-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e433-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3e433-442">Podrobné pokyny k vytvoření projektu Razor Pages najdete v tématu [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="3e433-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3e433-443">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="3e433-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3e433-444">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3e433-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="3e433-445">Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="3e433-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3e433-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3e433-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3e433-447">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="3e433-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="3e433-448">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-448">Razor Pages</span></span>

<span data-ttu-id="3e433-449">Razor Pages je povolený v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3e433-449">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="3e433-450">Zvažte základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="3e433-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="3e433-451">Předchozí kód vypadá podobně jako [soubor zobrazení Razor](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="3e433-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="3e433-452">To znamená, že tato `@page` direktiva je odlišná.</span><span class="sxs-lookup"><span data-stu-id="3e433-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="3e433-453">`@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler.</span><span class="sxs-lookup"><span data-stu-id="3e433-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="3e433-454">`@page`musí se jednat o první direktivu Razor na stránce.</span><span class="sxs-lookup"><span data-stu-id="3e433-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="3e433-455">`@page`má vliv na chování jiných konstrukcí Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="3e433-456">Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="3e433-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="3e433-457">Soubor *Pages/Index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="3e433-458">Model stránky *stránky/Index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="3e433-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="3e433-459">Podle konvence má `PageModel` soubor třídy stejný název jako soubor stránky Razor s *příponou. cs* .</span><span class="sxs-lookup"><span data-stu-id="3e433-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="3e433-460">Například předchozí stránka Razor je *Pages/Index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e433-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="3e433-461">Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="3e433-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="3e433-462">Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="3e433-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="3e433-463">Následující tabulka ukazuje cestu stránky Razor a adresu URL pro porovnání:</span><span class="sxs-lookup"><span data-stu-id="3e433-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="3e433-464">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="3e433-464">File name and path</span></span>               | <span data-ttu-id="3e433-465">shodná adresa URL</span><span class="sxs-lookup"><span data-stu-id="3e433-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3e433-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="3e433-467">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="3e433-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="3e433-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="3e433-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="3e433-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="3e433-471">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="3e433-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="3e433-472">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="3e433-472">Notes:</span></span>

* <span data-ttu-id="3e433-473">Modul runtime ve výchozím nastavení vyhledá soubory Razor Pages ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="3e433-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="3e433-474">`Index`je výchozí stránka, když adresa URL neobsahuje stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="3e433-475">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="3e433-475">Write a basic form</span></span>

<span data-ttu-id="3e433-476">Razor Pages je navržený tak, aby se při vytváření aplikace usnadnily běžné vzory používané s webovými prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="3e433-476">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="3e433-477">[Vázání modelů](xref:mvc/models/model-binding), [pomocníkům značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníkům, kteří *pracují pouze* s vlastnostmi definovanými ve třídě stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="3e433-478">Zvažte stránku, která pro `Contact` model implementuje základní formulář "kontaktujte nás":</span><span class="sxs-lookup"><span data-stu-id="3e433-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="3e433-479">Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .</span><span class="sxs-lookup"><span data-stu-id="3e433-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="3e433-480">Datový model:</span><span class="sxs-lookup"><span data-stu-id="3e433-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="3e433-481">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="3e433-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="3e433-482">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="3e433-483">Model stránky *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="3e433-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="3e433-484">Podle konvence je `PageModel` třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="3e433-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="3e433-485">`PageModel` Třída umožňuje oddělení logiky stránky od její prezentace.</span><span class="sxs-lookup"><span data-stu-id="3e433-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="3e433-486">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="3e433-487">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="3e433-487">This separation allows:</span></span>

* <span data-ttu-id="3e433-488">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3e433-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="3e433-489">[Testování částí](xref:test/razor-pages-tests) stránky</span><span class="sxs-lookup"><span data-stu-id="3e433-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="3e433-490">Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle).</span><span class="sxs-lookup"><span data-stu-id="3e433-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="3e433-491">Můžete přidat metody obslužné rutiny pro jakýkoli příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="3e433-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="3e433-492">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="3e433-492">The most common handlers are:</span></span>

* <span data-ttu-id="3e433-493">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="3e433-494">Ukázka [OnGet](#OnGet)</span><span class="sxs-lookup"><span data-stu-id="3e433-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="3e433-495">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="3e433-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="3e433-496">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="3e433-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="3e433-497">Předchozí kód je typický pro Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3e433-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="3e433-498">Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3e433-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="3e433-499">`OnPostAsync` Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.</span><span class="sxs-lookup"><span data-stu-id="3e433-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="3e433-500">Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation), [ověřování](xref:mvc/models/validation)a výsledky akce, jsou sdílené.</span><span class="sxs-lookup"><span data-stu-id="3e433-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="3e433-501">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="3e433-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="3e433-502">Základní tok `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="3e433-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="3e433-503">Kontrola chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="3e433-503">Check for validation errors.</span></span>

* <span data-ttu-id="3e433-504">V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.</span><span class="sxs-lookup"><span data-stu-id="3e433-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="3e433-505">Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami.</span><span class="sxs-lookup"><span data-stu-id="3e433-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="3e433-506">Ověřování na straně klienta je stejné jako tradiční aplikace ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="3e433-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="3e433-507">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="3e433-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="3e433-508">Po úspěšném zadání dat zavolá metoda `OnPostAsync` obslužné rutiny `RedirectToPage` pomocnou metodu, která vrátí instanci `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="3e433-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="3e433-509">`RedirectToPage`je nový výsledek akce, který `RedirectToAction` se podobá `RedirectToRoute`nebo, ale přizpůsobený pro stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="3e433-510">V předchozí ukázce přesměruje na stránku kořenového indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="3e433-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="3e433-511">`RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="3e433-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="3e433-512">Když odeslaný formulář obsahuje chyby ověřování (které jsou předány serveru), metoda`OnPostAsync` obslužné rutiny volá `Page` pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="3e433-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="3e433-513">`Page` vrací instanci `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="3e433-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="3e433-514">Vrácení `Page` se podobá tomu, jak vrátí `View`akce v řadičích.</span><span class="sxs-lookup"><span data-stu-id="3e433-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="3e433-515">`PageResult`je výchozí návratový typ pro metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3e433-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="3e433-516">Metoda obslužné rutiny, `void` která vrací vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="3e433-517">`Customer` Vlastnost používá `[BindProperty]` atribut pro přihlášení k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="3e433-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="3e433-518">Razor Pages ve výchozím nastavení vlastnosti BIND pouze s jinými než`GET` slovesy.</span><span class="sxs-lookup"><span data-stu-id="3e433-518">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="3e433-519">Vazba na vlastnosti může snížit množství kódu, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="3e433-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="3e433-520">Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře (`<input asp-for="Customer.Name">`) a přijměte vstup.</span><span class="sxs-lookup"><span data-stu-id="3e433-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="3e433-521">Domovská stránka (*index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="3e433-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="3e433-522">Přidružená `PageModel` třída (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3e433-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="3e433-523">Soubor *index. cshtml* obsahuje následující kód pro vytvoření odkazu pro úpravy pro každý kontakt:</span><span class="sxs-lookup"><span data-stu-id="3e433-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="3e433-524">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="3e433-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="3e433-525">Odkaz obsahuje data směrování s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="3e433-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="3e433-526">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="3e433-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="3e433-527">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="3e433-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="3e433-528">Pomocník značek je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="3e433-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="3e433-529">Soubor *Pages/Edit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="3e433-530">První řádek obsahuje `@page "{id:int}"` direktivu.</span><span class="sxs-lookup"><span data-stu-id="3e433-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="3e433-531">Omezení`"{id:int}"` směrování instruuje stránku, aby přijímala požadavky na stránku, která `int` obsahuje data směrování.</span><span class="sxs-lookup"><span data-stu-id="3e433-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="3e433-532">Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int`, modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="3e433-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="3e433-533">Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="3e433-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="3e433-534">Soubor *Pages/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="3e433-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="3e433-535">Soubor *index. cshtml* také obsahuje značky pro vytvoření tlačítka Odstranit pro kontaktování každého zákazníka:</span><span class="sxs-lookup"><span data-stu-id="3e433-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="3e433-536">Když je tlačítko Odstranit vykresleno ve formátu HTML, `formaction` obsahuje parametry pro:</span><span class="sxs-lookup"><span data-stu-id="3e433-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="3e433-537">ID kontaktu zákazníka určené `asp-route-id` atributem</span><span class="sxs-lookup"><span data-stu-id="3e433-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="3e433-538">`handler` Zadané `asp-page-handler` atributem.</span><span class="sxs-lookup"><span data-stu-id="3e433-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="3e433-539">Tady je příklad vygenerovaného tlačítka odstranit s ID kontaktu zákazníka `1`:</span><span class="sxs-lookup"><span data-stu-id="3e433-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="3e433-540">Když je vybráno tlačítko, pošle `POST` se na server požadavek na formulář.</span><span class="sxs-lookup"><span data-stu-id="3e433-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="3e433-541">Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu. `OnPost[handler]Async`</span><span class="sxs-lookup"><span data-stu-id="3e433-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="3e433-542">Vzhledem k `handler` tomu `delete` , že je v tomto `OnPostDeleteAsync` příkladu, metoda obslužné rutiny se `POST` používá ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="3e433-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="3e433-543">Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove`, je vybrána metoda obslužné rutiny s názvem. `OnPostRemoveAsync`</span><span class="sxs-lookup"><span data-stu-id="3e433-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="3e433-544">Následující kód ukazuje `OnPostDeleteAsync` obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="3e433-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="3e433-545">`OnPostDeleteAsync` Metoda:</span><span class="sxs-lookup"><span data-stu-id="3e433-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="3e433-546">Akceptuje `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="3e433-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="3e433-547">Pokud direktiva stránky *index. cshtml* obsahuje omezení `"{id:int?}"`směrování, `id` pocházela z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="3e433-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="3e433-548">Data trasy pro `id` se zadává v IDENTIFIKÁTORu URI, `https://localhost:5001/Customers/2`jako je například.</span><span class="sxs-lookup"><span data-stu-id="3e433-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="3e433-549">Dotazuje databázi na kontaktování zákazníka `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="3e433-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="3e433-550">Pokud se kontakt zákazníka najde, odebere se ze seznamu kontaktů zákazníka.</span><span class="sxs-lookup"><span data-stu-id="3e433-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="3e433-551">Databáze je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="3e433-551">The database is updated.</span></span>
* <span data-ttu-id="3e433-552">Volání `RedirectToPage` pro přesměrování na stránku kořenového indexu (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="3e433-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="3e433-553">Označit vlastnosti stránky jako povinné</span><span class="sxs-lookup"><span data-stu-id="3e433-553">Mark page properties as required</span></span>

<span data-ttu-id="3e433-554">Vlastnosti v `PageModel` lze označit [požadovaným](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atributem:</span><span class="sxs-lookup"><span data-stu-id="3e433-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="3e433-555">Další informace najdete v tématu [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3e433-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="3e433-556">Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet</span><span class="sxs-lookup"><span data-stu-id="3e433-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="3e433-557">`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="3e433-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="3e433-558">Na rozdíl `GET` od požadavků `HEAD` požadavky nevrátí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3e433-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="3e433-559">Obvykle je `OnHead` obslužná rutina vytvořena a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="3e433-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="3e433-560">V ASP.NET Core 2,1 nebo novější Razor Pages návrat k volání `OnGet` obslužné rutiny, pokud není `OnHead` definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="3e433-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="3e433-561">Toto chování je povoleno voláním [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3e433-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="3e433-562">Výchozí šablony generují `SetCompatibilityVersion` volání v ASP.NET Core 2,1 a 2,2.</span><span class="sxs-lookup"><span data-stu-id="3e433-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="3e433-563">`SetCompatibilityVersion`efektivně nastaví možnost `AllowMappingHeadRequestsToGetHandler` Razor Pages na `true`.</span><span class="sxs-lookup"><span data-stu-id="3e433-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="3e433-564">Místo toho, abyste se rozhodli pro všechna `SetCompatibilityVersion`chování pomocí, se můžete výslovně vyjádřit ke *konkrétnímu* chování.</span><span class="sxs-lookup"><span data-stu-id="3e433-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="3e433-565">Následující kód výslovný v pro povolení `HEAD` mapování požadavků na `OnGet` obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="3e433-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="3e433-566">XSRF/CSRF a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="3e433-567">Nemusíte psát žádný kód pro [ověřování proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="3e433-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="3e433-568">Generování a ověření tokenu antipadělání jsou automaticky zahrnuty do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="3e433-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="3e433-569">Použití rozložení, částečných, šablon a značek pomocníků s Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3e433-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="3e433-570">Stránky fungují se všemi možnostmi modulu zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="3e433-571">Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*, *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="3e433-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="3e433-572">Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="3e433-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="3e433-573">Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3e433-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="3e433-574">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="3e433-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="3e433-575">Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).</span><span class="sxs-lookup"><span data-stu-id="3e433-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="3e433-576">Importuje struktury HTML, jako jsou JavaScript a StyleSheet.</span><span class="sxs-lookup"><span data-stu-id="3e433-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="3e433-577">Další informace najdete na [stránce rozložení](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="3e433-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="3e433-578">Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3e433-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="3e433-579">Rozložení se nachází na *stránkách nebo ve sdílené* složce.</span><span class="sxs-lookup"><span data-stu-id="3e433-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="3e433-580">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="3e433-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="3e433-581">Rozložení na *stránkách nebo ve sdílené* složce se dá použít na libovolné stránce Razor ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="3e433-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="3e433-582">Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="3e433-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="3e433-583">Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="3e433-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="3e433-584">*Zobrazení/Shared* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="3e433-584">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="3e433-585">Razor Pages mají spoléhat na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="3e433-585">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="3e433-586">Zobrazení hledání ze stránky Razor obsahuje složku *stránky* .</span><span class="sxs-lookup"><span data-stu-id="3e433-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="3e433-587">Rozložení, šablony a částečně používané s řadiči MVC a konvenčními zobrazeními Razor *fungují pouze*.</span><span class="sxs-lookup"><span data-stu-id="3e433-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="3e433-588">Přidejte soubor *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="3e433-589">`@namespace`je vysvětleno dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="3e433-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="3e433-590">Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="3e433-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="3e433-591">V případě `@namespace` explicitního použití direktivy na stránce:</span><span class="sxs-lookup"><span data-stu-id="3e433-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="3e433-592">Direktiva nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="3e433-593">`@model` Direktiva nemusí zahrnovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="3e433-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="3e433-594">Pokud je `@namespace` direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu.</span><span class="sxs-lookup"><span data-stu-id="3e433-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="3e433-595">Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="3e433-596">Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="3e433-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="3e433-597">Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="3e433-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="3e433-598">Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` třída.</span><span class="sxs-lookup"><span data-stu-id="3e433-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="3e433-599">`@namespace`*funguje také se konvenčními zobrazeními Razor.*</span><span class="sxs-lookup"><span data-stu-id="3e433-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="3e433-600">Soubor zobrazení původní *stránky/vytvořit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="3e433-601">Aktualizovaný soubor zobrazení *stránky/vytvoření. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e433-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="3e433-602">[Projekt Razor Pages Starter](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které zapojování ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3e433-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="3e433-603">Další informace o částečných zobrazeních naleznete <xref:mvc/views/partial>v tématu.</span><span class="sxs-lookup"><span data-stu-id="3e433-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="3e433-604">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="3e433-604">URL generation for Pages</span></span>

<span data-ttu-id="3e433-605">Stránka `Create` , která se zobrazuje dřív, `RedirectToPage`používá:</span><span class="sxs-lookup"><span data-stu-id="3e433-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="3e433-606">Aplikace má následující strukturu souborů nebo složek:</span><span class="sxs-lookup"><span data-stu-id="3e433-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="3e433-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="3e433-607">*/Pages*</span></span>

  * <span data-ttu-id="3e433-608">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="3e433-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="3e433-609">*/Customers*</span></span>

    * <span data-ttu-id="3e433-610">*Vytvořit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="3e433-611">*Upravit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="3e433-612">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="3e433-612">*Index.cshtml*</span></span>

<span data-ttu-id="3e433-613">*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit.* cshtml Pages přesměruje na *pages/index. cshtml* po úspěchu.</span><span class="sxs-lookup"><span data-stu-id="3e433-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="3e433-614">Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce.</span><span class="sxs-lookup"><span data-stu-id="3e433-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="3e433-615">Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3e433-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="3e433-616">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3e433-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="3e433-617">Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index`).</span><span class="sxs-lookup"><span data-stu-id="3e433-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="3e433-618">Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkce, které se zakódujeme na adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3e433-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="3e433-619">Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="3e433-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="3e433-620">Generování adresy URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="3e433-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="3e433-621">Následující tabulka ukazuje, která stránka indexu je vybrána s různými `RedirectToPage` parametry ze *stránky/zákazníci/vytvořit. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3e433-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="3e433-622">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="3e433-622">RedirectToPage(x)</span></span>| <span data-ttu-id="3e433-623">stránka</span><span class="sxs-lookup"><span data-stu-id="3e433-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="3e433-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="3e433-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="3e433-625">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-625">*Pages/Index*</span></span> |
| <span data-ttu-id="3e433-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="3e433-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="3e433-627">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="3e433-628">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="3e433-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="3e433-629">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-629">*Pages/Index*</span></span> |
| <span data-ttu-id="3e433-630">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="3e433-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="3e433-631">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="3e433-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="3e433-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="3e433-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="3e433-633">`RedirectToPage` Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="3e433-634">Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="3e433-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="3e433-635">Pokud k propojení mezi stránkami ve složce použijete relativní názvy, můžete tuto složku přejmenovat.</span><span class="sxs-lookup"><span data-stu-id="3e433-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="3e433-636">Všechny odkazy pořád fungují (protože neobsahují název složky).</span><span class="sxs-lookup"><span data-stu-id="3e433-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="3e433-637">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="3e433-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="3e433-638">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="3e433-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="3e433-639">ViewData – atribut</span><span class="sxs-lookup"><span data-stu-id="3e433-639">ViewData attribute</span></span>

<span data-ttu-id="3e433-640">Data je možné předat na stránku pomocí [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="3e433-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="3e433-641">Vlastnosti v řadičích Razor nebo modelech stránek `[ViewData]` s atributem mají své hodnoty uložené a načtené z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="3e433-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="3e433-642">V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost s `[ViewData]`označením.</span><span class="sxs-lookup"><span data-stu-id="3e433-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="3e433-643">`Title` Vlastnost je nastavena na název stránky o produktu:</span><span class="sxs-lookup"><span data-stu-id="3e433-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="3e433-644">Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="3e433-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="3e433-645">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="3e433-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="3e433-646">TempData</span><span class="sxs-lookup"><span data-stu-id="3e433-646">TempData</span></span>

<span data-ttu-id="3e433-647">ASP.NET Core zpřístupňuje vlastnost [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="3e433-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="3e433-648">Tato vlastnost ukládá data do jejich čtení.</span><span class="sxs-lookup"><span data-stu-id="3e433-648">This property stores data until it's read.</span></span> <span data-ttu-id="3e433-649">Metody `Keep` a `Peek` lze použít k prohlédnutí dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="3e433-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="3e433-650">`TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="3e433-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="3e433-651">Následující kód nastaví hodnotu `Message` pomocí: `TempData`</span><span class="sxs-lookup"><span data-stu-id="3e433-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="3e433-652">Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData`.</span><span class="sxs-lookup"><span data-stu-id="3e433-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="3e433-653">Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3e433-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="3e433-654">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="3e433-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="3e433-655">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="3e433-655">Multiple handlers per page</span></span>

<span data-ttu-id="3e433-656">Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="3e433-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="3e433-657">Formulář v předchozím příkladu obsahuje dvě tlačítka `FormActionTagHelper` pro odeslání, z nichž každá používá pro odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="3e433-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="3e433-658">`asp-page-handler` Atribut je doprovodný objekt k `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="3e433-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="3e433-659">`asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou.</span><span class="sxs-lookup"><span data-stu-id="3e433-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="3e433-660">`asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="3e433-661">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="3e433-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="3e433-662">Předchozí kód používá *pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="3e433-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="3e433-663">Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="3e433-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="3e433-664">V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="3e433-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="3e433-665">Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="3e433-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="3e433-666">Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` kterou se odesílá,. `https://localhost:5001/Customers/CreateFATH?handler=JoinList`</span><span class="sxs-lookup"><span data-stu-id="3e433-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="3e433-667">Cesta URL, na `OnPostJoinListUCAsync` kterou se `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`odesílá.</span><span class="sxs-lookup"><span data-stu-id="3e433-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="3e433-668">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="3e433-668">Custom routes</span></span>

<span data-ttu-id="3e433-669">Použijte tuto `@page` direktivu pro:</span><span class="sxs-lookup"><span data-stu-id="3e433-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="3e433-670">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="3e433-670">Specify a custom route to a page.</span></span> <span data-ttu-id="3e433-671">Například trasa na stránku o aplikaci `/Some/Other/Path` `@page "/Some/Other/Path"`může být nastavena na.</span><span class="sxs-lookup"><span data-stu-id="3e433-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="3e433-672">Připojit segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-672">Append segments to a page's default route.</span></span> <span data-ttu-id="3e433-673">Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="3e433-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="3e433-674">Připojení parametrů k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="3e433-675">Například parametr `id`ID může být požadován pro stránku s `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="3e433-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="3e433-676">Je podporována kořenová relativní cesta, která je určena`~`vlnovkou () na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="3e433-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="3e433-677">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="3e433-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="3e433-678">Pokud se v adrese URL nelíbí `?handler=JoinList` řetězec dotazu, změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="3e433-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="3e433-679">Trasa se dá přizpůsobit přidáním šablony trasy, která je `@page` uzavřená do dvojitých uvozovek za direktivou.</span><span class="sxs-lookup"><span data-stu-id="3e433-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="3e433-680">Pomocí předchozího kódu je cesta URL, na `OnPostJoinListAsync` kterou se odesílá,. `https://localhost:5001/Customers/CreateFATH/JoinList`</span><span class="sxs-lookup"><span data-stu-id="3e433-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="3e433-681">Cesta URL, na `OnPostJoinListUCAsync` kterou se `https://localhost:5001/Customers/CreateFATH/JoinListUC`odesílá.</span><span class="sxs-lookup"><span data-stu-id="3e433-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="3e433-682">`?` Následující `handler` údaj znamená, že parametr trasy je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="3e433-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="3e433-683">Konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="3e433-683">Configuration and settings</span></span>

<span data-ttu-id="3e433-684">Pokud chcete konfigurovat pokročilé možnosti, použijte metodu `AddRazorPagesOptions` rozšíření v Tvůrci MVC:</span><span class="sxs-lookup"><span data-stu-id="3e433-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="3e433-685">V `RazorPagesOptions` současné době můžete použít k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="3e433-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="3e433-686">Tímto způsobem v budoucnu povolíme lepší rozšíření.</span><span class="sxs-lookup"><span data-stu-id="3e433-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="3e433-687">Chcete-li předkompilovat zobrazení, přečtěte si téma [ Razor zobrazení kompilace](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="3e433-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="3e433-688">[Stažení nebo zobrazení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="3e433-688">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="3e433-689">Viz Začínáme [ Razor se stránkami](xref:tutorials/razor-pages/razor-pages-start), které jsou založeny na tomto úvodu.</span><span class="sxs-lookup"><span data-stu-id="3e433-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="3e433-690">Zadejte, Razor jestli se stránky nacházejí v kořenu obsahu.</span><span class="sxs-lookup"><span data-stu-id="3e433-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="3e433-691">Ve výchozím nastavení Razor se stránky rootují v adresáři */Pages* .</span><span class="sxs-lookup"><span data-stu-id="3e433-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="3e433-692">Přidejte [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, jestli Razor jsou vaše stránky v [kořenu obsahu](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:</span><span class="sxs-lookup"><span data-stu-id="3e433-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="3e433-693">Zadejte, Razor jestli jsou stránky ve vlastním kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="3e433-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="3e433-694">Přidejte [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) , abyste určili Razor , že vaše stránky jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="3e433-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="3e433-695">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="3e433-695">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
