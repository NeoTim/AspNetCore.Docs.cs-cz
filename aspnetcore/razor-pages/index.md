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
ms.openlocfilehash: bd9f991a2aba32cbbeb193ad422005f910e6795b
ms.sourcegitcommit: ca6a1f100c1a3f59999189aa962523442dd4ead1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87444068"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="294fb-103">Úvod do Razor stránek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="294fb-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="294fb-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="294fb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="294fb-105">RazorStránky můžou vytvářet kódovací scénáře zaměřené na stránky a lépe a produktivnější než používání řadičů a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="294fb-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="294fb-106">Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="294fb-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="294fb-107">Tento dokument popisuje úvodní Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="294fb-108">Nejedná se o podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="294fb-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="294fb-109">Pokud zjistíte, že některé části jsou moc pokročilé, přečtěte si téma Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="294fb-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="294fb-110">Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="294fb-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="294fb-111">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="294fb-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="294fb-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="294fb-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="294fb-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="294fb-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="294fb-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="294fb-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="294fb-115">Vytvořit Razor projekt stránek</span><span class="sxs-lookup"><span data-stu-id="294fb-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="294fb-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="294fb-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="294fb-117">Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="294fb-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="294fb-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="294fb-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="294fb-119">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="294fb-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="294fb-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="294fb-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="294fb-121">Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="294fb-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="294fb-122">RazorStránky</span><span class="sxs-lookup"><span data-stu-id="294fb-122">Razor Pages</span></span>

<span data-ttu-id="294fb-123">RazorStránky jsou povolené v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="294fb-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="294fb-124">Zvažte základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="294fb-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="294fb-125">Předchozí kód vypadá jako [ Razor soubor zobrazení](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="294fb-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="294fb-126">To znamená, že tato direktiva je odlišná [`@page`](xref:mvc/views/razor#page) .</span><span class="sxs-lookup"><span data-stu-id="294fb-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="294fb-127">`@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler.</span><span class="sxs-lookup"><span data-stu-id="294fb-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="294fb-128">`@page`musí se jednat o první Razor direktivu na stránce.</span><span class="sxs-lookup"><span data-stu-id="294fb-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="294fb-129">`@page`má vliv na chování jiných [Razor](xref:mvc/views/razor) konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="294fb-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="294fb-130">RazorNázvy souborů stránek mají příponu *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="294fb-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="294fb-131">Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="294fb-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="294fb-132">Soubor *Pages/Index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="294fb-133">Model stránky *stránky/Index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="294fb-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="294fb-134">Podle konvence `PageModel` má soubor třídy stejný název jako Razor stránkovací soubor s *příponou. cs* .</span><span class="sxs-lookup"><span data-stu-id="294fb-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="294fb-135">Například předchozí Razor stránka je *Pages/Index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="294fb-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="294fb-136">Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="294fb-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="294fb-137">Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="294fb-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="294fb-138">Následující tabulka ukazuje Razor cestu stránky a adresu URL pro porovnání:</span><span class="sxs-lookup"><span data-stu-id="294fb-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="294fb-139">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="294fb-139">File name and path</span></span>               | <span data-ttu-id="294fb-140">shodná adresa URL</span><span class="sxs-lookup"><span data-stu-id="294fb-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="294fb-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="294fb-142">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="294fb-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="294fb-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="294fb-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="294fb-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="294fb-146">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="294fb-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="294fb-147">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="294fb-147">Notes:</span></span>

* <span data-ttu-id="294fb-148">Modul runtime Razor ve výchozím nastavení vyhledá soubory stránek ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="294fb-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="294fb-149">`Index`je výchozí stránka, když adresa URL neobsahuje stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="294fb-150">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="294fb-150">Write a basic form</span></span>

<span data-ttu-id="294fb-151">RazorStránky jsou navržené tak, aby při vytváření aplikace byly běžné vzory používané s webovými prohlížeči, které se dají snadno implementovat.</span><span class="sxs-lookup"><span data-stu-id="294fb-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="294fb-152">[Vazba modelů](xref:mvc/models/model-binding), [pomocníky značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníků pro HTML *stačí pracovat* s vlastnostmi definovanými ve Razor třídě stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="294fb-153">Zvažte stránku, která pro model implementuje základní formulář "kontaktujte nás" `Contact` :</span><span class="sxs-lookup"><span data-stu-id="294fb-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="294fb-154">Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) .</span><span class="sxs-lookup"><span data-stu-id="294fb-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="294fb-155">Databáze v paměti vyžaduje `Microsoft.EntityFrameworkCore.InMemory` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="294fb-155">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="294fb-156">Datový model:</span><span class="sxs-lookup"><span data-stu-id="294fb-156">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="294fb-157">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="294fb-157">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="294fb-158">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="294fb-159">Model stránky *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="294fb-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="294fb-160">Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="294fb-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="294fb-161">`PageModel`Třída umožňuje oddělení logiky stránky od její prezentace.</span><span class="sxs-lookup"><span data-stu-id="294fb-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="294fb-162">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="294fb-163">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="294fb-163">This separation allows:</span></span>

* <span data-ttu-id="294fb-164">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="294fb-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="294fb-165">Testování jednotek</span><span class="sxs-lookup"><span data-stu-id="294fb-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="294fb-166">Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle).</span><span class="sxs-lookup"><span data-stu-id="294fb-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="294fb-167">Lze přidat metody obslužné rutiny pro jakýkoli příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="294fb-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="294fb-168">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="294fb-168">The most common handlers are:</span></span>

* <span data-ttu-id="294fb-169">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="294fb-170">V předchozím kódu `OnGet` Metoda zobrazí stránku *CreateModel. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="294fb-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="294fb-171">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="294fb-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="294fb-172">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="294fb-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="294fb-173">Předchozí kód je typický pro Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="294fb-174">Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="294fb-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="294fb-175">`OnPostAsync`Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.</span><span class="sxs-lookup"><span data-stu-id="294fb-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="294fb-176">Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation)a výsledky akce, fungují stejně jako řadiče a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="294fb-177">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="294fb-177">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="294fb-178">Základní tok `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="294fb-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="294fb-179">Kontrola chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="294fb-179">Check for validation errors.</span></span>

* <span data-ttu-id="294fb-180">V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.</span><span class="sxs-lookup"><span data-stu-id="294fb-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="294fb-181">Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami.</span><span class="sxs-lookup"><span data-stu-id="294fb-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="294fb-182">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="294fb-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="294fb-183">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="294fb-184">Vykreslený HTML ze *stránky/vytvořit. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="294fb-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="294fb-185">V předchozím kódu vyúčtováním formuláře:</span><span class="sxs-lookup"><span data-stu-id="294fb-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="294fb-186">S platnými daty:</span><span class="sxs-lookup"><span data-stu-id="294fb-186">With valid data:</span></span>

  * <span data-ttu-id="294fb-187">`OnPostAsync`Metoda obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="294fb-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="294fb-188">`RedirectToPage` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="294fb-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="294fb-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="294fb-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="294fb-190">Je výsledkem akce.</span><span class="sxs-lookup"><span data-stu-id="294fb-190">Is an action result.</span></span>
    * <span data-ttu-id="294fb-191">Je podobný `RedirectToAction` nebo `RedirectToRoute` (používá se v řadičích a zobrazeních).</span><span class="sxs-lookup"><span data-stu-id="294fb-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="294fb-192">Je přizpůsoben pro stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-192">Is customized for pages.</span></span> <span data-ttu-id="294fb-193">V předchozí ukázce přesměruje na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="294fb-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="294fb-194">`RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="294fb-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="294fb-195">S chybami ověřování, které jsou předány serveru:</span><span class="sxs-lookup"><span data-stu-id="294fb-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="294fb-196">`OnPostAsync`Metoda obslužné rutiny volá <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="294fb-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="294fb-197">`Page` vrací instanci <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="294fb-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="294fb-198">Vrácení `Page` se podobá tomu, jak vrátí akce v řadičích `View` .</span><span class="sxs-lookup"><span data-stu-id="294fb-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="294fb-199">`PageResult`je výchozí návratový typ pro metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="294fb-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="294fb-200">Metoda obslužné rutiny, která vrací `void` vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="294fb-201">V předchozím příkladu publikování formuláře bez výsledků hodnoty v [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) vrací hodnotu false.</span><span class="sxs-lookup"><span data-stu-id="294fb-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="294fb-202">V této ukázce nejsou v klientovi zobrazeny žádné chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="294fb-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="294fb-203">V tomto dokumentu se pojednává o chybách ověřování.</span><span class="sxs-lookup"><span data-stu-id="294fb-203">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="294fb-204">S chybami ověřování zjištěnými ověřováním na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="294fb-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="294fb-205">Data **nejsou** odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="294fb-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="294fb-206">Ověřování na straně klienta je vysvětleno dále v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="294fb-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="294fb-207">`Customer`Vlastnost používá [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) atribut pro výslovný souhlas s vazbou modelu:</span><span class="sxs-lookup"><span data-stu-id="294fb-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="294fb-208">`[BindProperty]`neměl **by se** používat pro modely obsahující vlastnosti, které by klient neměl měnit.</span><span class="sxs-lookup"><span data-stu-id="294fb-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="294fb-209">Další informace najdete v tématu [přestavení](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="294fb-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="294fb-210">RazorStránky, ve výchozím nastavení vlastnosti BIND pouze bez `GET` slovesa.</span><span class="sxs-lookup"><span data-stu-id="294fb-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="294fb-211">Vazba na vlastnosti odstraní nutnost psaní kódu pro převod dat HTTP na typ modelu.</span><span class="sxs-lookup"><span data-stu-id="294fb-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="294fb-212">Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře ( `<input asp-for="Customer.Name">` ) a přijměte vstup.</span><span class="sxs-lookup"><span data-stu-id="294fb-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="294fb-213">Prohlíží se soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="294fb-214">V předchozím kódu [Pomocník vstupní značky](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` váže `<input>` prvek HTML na `Customer.Name` výraz modelu.</span><span class="sxs-lookup"><span data-stu-id="294fb-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="294fb-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)zpřístupňuje pomocníkům značky.</span><span class="sxs-lookup"><span data-stu-id="294fb-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="294fb-216">Domovská stránka</span><span class="sxs-lookup"><span data-stu-id="294fb-216">The home page</span></span>

<span data-ttu-id="294fb-217">*Index. cshtml* je Domovská stránka:</span><span class="sxs-lookup"><span data-stu-id="294fb-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="294fb-218">Přidružená `PageModel` třída (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="294fb-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="294fb-219">Soubor *index. cshtml* obsahuje následující kód:</span><span class="sxs-lookup"><span data-stu-id="294fb-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="294fb-220">`<a /a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="294fb-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="294fb-221">Odkaz obsahuje data směrování s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="294fb-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="294fb-222">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="294fb-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="294fb-223">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="294fb-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="294fb-224">Soubor *index. cshtml* obsahuje značky pro vytvoření tlačítka Odstranit pro každého kontaktu zákazníka:</span><span class="sxs-lookup"><span data-stu-id="294fb-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="294fb-225">Vykreslený kód HTML:</span><span class="sxs-lookup"><span data-stu-id="294fb-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="294fb-226">Pokud je tlačítko Odstranit vykresleno ve formátu HTML, obsahuje [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) parametry pro:</span><span class="sxs-lookup"><span data-stu-id="294fb-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="294fb-227">ID kontaktu zákazníka určené `asp-route-id` atributem.</span><span class="sxs-lookup"><span data-stu-id="294fb-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="294fb-228">`handler`, Určené `asp-page-handler` atributem.</span><span class="sxs-lookup"><span data-stu-id="294fb-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="294fb-229">Když je vybráno tlačítko, `POST` pošle se na server požadavek na formulář.</span><span class="sxs-lookup"><span data-stu-id="294fb-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="294fb-230">Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="294fb-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="294fb-231">Vzhledem k `handler` `delete` tomu, že je v tomto příkladu, `OnPostDeleteAsync` Metoda obslužné rutiny se používá ke zpracování `POST` požadavku.</span><span class="sxs-lookup"><span data-stu-id="294fb-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="294fb-232">Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove` , je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="294fb-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="294fb-233">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="294fb-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="294fb-234">Získá `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="294fb-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="294fb-235">Dotazuje databázi na kontaktování zákazníka `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="294fb-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="294fb-236">Pokud se kontakt zákazníka najde, odebere se a aktualizuje se databáze.</span><span class="sxs-lookup"><span data-stu-id="294fb-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="294fb-237">Volání <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pro přesměrování na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="294fb-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="294fb-238">Upravit soubor. cshtml</span><span class="sxs-lookup"><span data-stu-id="294fb-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="294fb-239">První řádek obsahuje `@page "{id:int}"` direktivu.</span><span class="sxs-lookup"><span data-stu-id="294fb-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="294fb-240">Omezení směrování `"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje `int` data směrování.</span><span class="sxs-lookup"><span data-stu-id="294fb-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="294fb-241">Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int` , modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="294fb-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="294fb-242">Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="294fb-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="294fb-243">Soubor *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="294fb-243">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="294fb-244">Ověřování</span><span class="sxs-lookup"><span data-stu-id="294fb-244">Validation</span></span>

<span data-ttu-id="294fb-245">Ověřovací pravidla:</span><span class="sxs-lookup"><span data-stu-id="294fb-245">Validation rules:</span></span>

* <span data-ttu-id="294fb-246">Jsou deklarativně určeny ve třídě modelu.</span><span class="sxs-lookup"><span data-stu-id="294fb-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="294fb-247">Jsou vynutily všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="294fb-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="294fb-248"><xref:System.ComponentModel.DataAnnotations>Obor názvů poskytuje sadu předdefinovaných ověřovacích atributů, které jsou aplikovány deklarativně na třídu nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="294fb-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="294fb-249">Tato dataanotace také obsahuje atributy formátování [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) , jako jsou tyto informace užitečné při formátování a neposkytují žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="294fb-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="294fb-250">Vezměte v úvahu `Customer` model:</span><span class="sxs-lookup"><span data-stu-id="294fb-250">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="294fb-251">Pomocí následujícího souboru zobrazení *Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="294fb-252">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="294fb-252">The preceding code:</span></span>

* <span data-ttu-id="294fb-253">Zahrnuje skripty pro ověření jQuery a jQuery.</span><span class="sxs-lookup"><span data-stu-id="294fb-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="294fb-254">Používá `<div />` `<span />` [pomocníky značek](xref:mvc/views/tag-helpers/intro) a k povolení:</span><span class="sxs-lookup"><span data-stu-id="294fb-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="294fb-255">Ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="294fb-255">Client-side validation.</span></span>
  * <span data-ttu-id="294fb-256">Vykreslování chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="294fb-256">Validation error rendering.</span></span>

* <span data-ttu-id="294fb-257">Generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="294fb-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="294fb-258">Při publikování formuláře vytvořit bez hodnoty Name se zobrazí chybová zpráva "pole název je povinné."</span><span class="sxs-lookup"><span data-stu-id="294fb-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="294fb-259">ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="294fb-259">on the form.</span></span> <span data-ttu-id="294fb-260">Pokud je v klientovi povolený jazyk JavaScript, zobrazí se v prohlížeči chyba bez odeslání na server.</span><span class="sxs-lookup"><span data-stu-id="294fb-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="294fb-261">`[StringLength(10)]`Atribut generuje `data-val-length-max="10"` na vykresleném HTML.</span><span class="sxs-lookup"><span data-stu-id="294fb-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="294fb-262">`data-val-length-max`zabrání prohlížečům v zadávání více než maximální zadané délky.</span><span class="sxs-lookup"><span data-stu-id="294fb-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="294fb-263">Pokud se k úpravě a přehrání příspěvku používá nástroj, například [Fiddler](https://www.telerik.com/fiddler) :</span><span class="sxs-lookup"><span data-stu-id="294fb-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="294fb-264">S názvem delším než 10.</span><span class="sxs-lookup"><span data-stu-id="294fb-264">With the name longer than 10.</span></span>
* <span data-ttu-id="294fb-265">Chybová zpráva "název pole musí být řetězec s maximální délkou 10."</span><span class="sxs-lookup"><span data-stu-id="294fb-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="294fb-266">.</span><span class="sxs-lookup"><span data-stu-id="294fb-266">is returned.</span></span>

<span data-ttu-id="294fb-267">Vezměte v úvahu následující `Movie` model:</span><span class="sxs-lookup"><span data-stu-id="294fb-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="294fb-268">Atributy ověřování určují chování, které se má vyhovět pro vlastnosti modelu, na které se aplikují:</span><span class="sxs-lookup"><span data-stu-id="294fb-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="294fb-269">`Required`Atributy a `MinimumLength` označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.</span><span class="sxs-lookup"><span data-stu-id="294fb-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="294fb-270">`RegularExpression`Atribut slouží k omezení znaků, které lze zadat.</span><span class="sxs-lookup"><span data-stu-id="294fb-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="294fb-271">V předchozím kódu "Žánr":</span><span class="sxs-lookup"><span data-stu-id="294fb-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="294fb-272">Je nutné použít pouze písmena.</span><span class="sxs-lookup"><span data-stu-id="294fb-272">Must only use letters.</span></span>
  * <span data-ttu-id="294fb-273">První písmeno musí být velkými písmeny.</span><span class="sxs-lookup"><span data-stu-id="294fb-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="294fb-274">Mezery, číslice a speciální znaky nejsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="294fb-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="294fb-275">`RegularExpression`Hodnocení:</span><span class="sxs-lookup"><span data-stu-id="294fb-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="294fb-276">Vyžaduje, aby byl první znak velkým písmenem.</span><span class="sxs-lookup"><span data-stu-id="294fb-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="294fb-277">Umožňuje speciální znaky a čísla v následujících mezerách.</span><span class="sxs-lookup"><span data-stu-id="294fb-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="294fb-278">"PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="294fb-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="294fb-279">Atribut `Range` omezuje hodnotu v konkrétním rozsahu.</span><span class="sxs-lookup"><span data-stu-id="294fb-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="294fb-280">`StringLength`Atribut nastaví maximální délku řetězcové vlastnosti a volitelně její minimální délku.</span><span class="sxs-lookup"><span data-stu-id="294fb-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="294fb-281">Typy hodnot (například `decimal` , `int` , `float` , `DateTime` ) jsou podstatně požadovány a nepotřebují `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="294fb-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="294fb-282">Stránka vytvořit pro `Movie` model zobrazuje chyby s neplatnými hodnotami:</span><span class="sxs-lookup"><span data-stu-id="294fb-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="294fb-284">Další informace:</span><span class="sxs-lookup"><span data-stu-id="294fb-284">For more information, see:</span></span>

* [<span data-ttu-id="294fb-285">Přidání ověření do aplikace Movie</span><span class="sxs-lookup"><span data-stu-id="294fb-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="294fb-286">[Ověřování modelu v ASP.NET Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="294fb-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="294fb-287">Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet</span><span class="sxs-lookup"><span data-stu-id="294fb-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="294fb-288">`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="294fb-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="294fb-289">Na rozdíl od `GET` požadavků `HEAD` požadavky nevrátí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="294fb-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="294fb-290">Obvykle `OnHead` je obslužná rutina vytvořena a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="294fb-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="294fb-291">RazorStránky se vrátí k volání `OnGet` obslužné rutiny, `OnHead` Pokud není definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="294fb-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="294fb-292">XSRF/CSRF a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="294fb-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="294fb-293">RazorStránky jsou chráněny [ověřováním proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="294fb-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="294fb-294">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) vloží tokeny proti padělání do prvků formuláře HTML.</span><span class="sxs-lookup"><span data-stu-id="294fb-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="294fb-295">Použití rozložení, částečných, šablon a značek pomocníka se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="294fb-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="294fb-296">Stránky fungují se všemi možnostmi Razor modulu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="294fb-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="294fb-297">Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*a *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních Razor zobrazení.</span><span class="sxs-lookup"><span data-stu-id="294fb-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="294fb-298">Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="294fb-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="294fb-299">Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="294fb-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="294fb-300">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="294fb-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="294fb-301">Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).</span><span class="sxs-lookup"><span data-stu-id="294fb-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="294fb-302">Importuje struktury HTML, jako jsou JavaScript a StyleSheet.</span><span class="sxs-lookup"><span data-stu-id="294fb-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="294fb-303">Obsah Razor stránky se vykreslí tam, kde `@RenderBody()` se volá.</span><span class="sxs-lookup"><span data-stu-id="294fb-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="294fb-304">Další informace najdete v tématu [rozložení stránky](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="294fb-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="294fb-305">Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="294fb-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="294fb-306">Rozložení se nachází na *stránkách nebo ve sdílené* složce.</span><span class="sxs-lookup"><span data-stu-id="294fb-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="294fb-307">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="294fb-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="294fb-308">Rozložení na *stránkách nebo ve sdílené* složce můžete použít na libovolné Razor stránce ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="294fb-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="294fb-309">Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="294fb-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="294fb-310">Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="294fb-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="294fb-311">*Zobrazení/Shared* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="294fb-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="294fb-312">RazorStránky mají sloužit k spoléhání se na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="294fb-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="294fb-313">Zobrazení hledání ze Razor stránky obsahuje složku *stránky* .</span><span class="sxs-lookup"><span data-stu-id="294fb-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="294fb-314">Rozložení, šablony a částečné typy používané s řadiči MVC a konvenčními Razor zobrazeními *fungují pouze*.</span><span class="sxs-lookup"><span data-stu-id="294fb-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="294fb-315">Přidejte soubor *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="294fb-316">`@namespace`je vysvětleno dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="294fb-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="294fb-317">Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="294fb-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="294fb-318">`@namespace`Direktiva nastavená na stránce:</span><span class="sxs-lookup"><span data-stu-id="294fb-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="294fb-319">`@namespace`Direktiva nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="294fb-320">`@model`Direktiva nemusí zahrnovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="294fb-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="294fb-321">Pokud `@namespace` je direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu.</span><span class="sxs-lookup"><span data-stu-id="294fb-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="294fb-322">Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="294fb-323">Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="294fb-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="294fb-324">Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="294fb-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="294fb-325">Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` Třída.</span><span class="sxs-lookup"><span data-stu-id="294fb-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="294fb-326">`@namespace`*funguje také s konvenčními Razor zobrazeními.*</span><span class="sxs-lookup"><span data-stu-id="294fb-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="294fb-327">Zvažte *stránku zobrazení stránky/vytvořit soubor. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="294fb-328">Aktualizovaný soubor zobrazení *Pages/Create. cshtml* s *_ViewImports. cshtml* a předchozím souborem rozložení:</span><span class="sxs-lookup"><span data-stu-id="294fb-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="294fb-329">V předchozím kódu *_ViewImports. cshtml* importovala obor názvů a pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="294fb-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="294fb-330">Soubor rozložení importoval soubory JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="294fb-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="294fb-331">[ Razor Úvodní projekt stránky](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které se připojí k ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="294fb-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="294fb-332">Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="294fb-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="294fb-333">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="294fb-333">URL generation for Pages</span></span>

<span data-ttu-id="294fb-334">`Create`Stránka, která se zobrazuje dřív, používá `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="294fb-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="294fb-335">Aplikace má následující strukturu souborů nebo složek:</span><span class="sxs-lookup"><span data-stu-id="294fb-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="294fb-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="294fb-336">*/Pages*</span></span>

  * <span data-ttu-id="294fb-337">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="294fb-338">*Soukromí. cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="294fb-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="294fb-339">*/Customers*</span></span>

    * <span data-ttu-id="294fb-340">*Vytvořit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="294fb-341">*Upravit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="294fb-342">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-342">*Index.cshtml*</span></span>

<span data-ttu-id="294fb-343">*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit. cshtml* Pages přesměruje na *Pages/Customers/index. cshtml* po úspěchu.</span><span class="sxs-lookup"><span data-stu-id="294fb-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="294fb-344">Řetězec `./Index` je relativní název stránky, který slouží k přístupu na předchozí stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="294fb-345">Slouží ke generování adres URL na stránce *Pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="294fb-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="294fb-346">Příklad:</span><span class="sxs-lookup"><span data-stu-id="294fb-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="294fb-347">Absolutní název stránky `/Index` se používá ke generování adres URL na stránce *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="294fb-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="294fb-348">Příklad:</span><span class="sxs-lookup"><span data-stu-id="294fb-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="294fb-349">Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="294fb-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="294fb-350">Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkční funkce v rámci hardwarového kódování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="294fb-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="294fb-351">Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="294fb-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="294fb-352">Generování adresy URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="294fb-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="294fb-353">Následující tabulka ukazuje, která stránka index je vybrána pomocí různých `RedirectToPage` parametrů na *stránkách/zákaznících/vytvořit. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="294fb-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="294fb-354">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="294fb-354">RedirectToPage(x)</span></span>| <span data-ttu-id="294fb-355">Stránka</span><span class="sxs-lookup"><span data-stu-id="294fb-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="294fb-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="294fb-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="294fb-357">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-357">*Pages/Index*</span></span> |
| <span data-ttu-id="294fb-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="294fb-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="294fb-359">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="294fb-360">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="294fb-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="294fb-361">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-361">*Pages/Index*</span></span> |
| <span data-ttu-id="294fb-362">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="294fb-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="294fb-363">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="294fb-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")` a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="294fb-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="294fb-365">`RedirectToPage`Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="294fb-366">Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="294fb-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="294fb-367">Když se k propojení mezi stránkami ve složce použijí relativní názvy:</span><span class="sxs-lookup"><span data-stu-id="294fb-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="294fb-368">Přejmenování složky neruší relativní odkazy.</span><span class="sxs-lookup"><span data-stu-id="294fb-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="294fb-369">Odkazy nejsou přerušeny, protože neobsahují název složky.</span><span class="sxs-lookup"><span data-stu-id="294fb-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="294fb-370">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="294fb-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="294fb-371">Další informace naleznete v tématech <xref:mvc/controllers/areas> a <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="294fb-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="294fb-372">ViewData – atribut</span><span class="sxs-lookup"><span data-stu-id="294fb-372">ViewData attribute</span></span>

<span data-ttu-id="294fb-373">Data je možné předat stránce pomocí <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="294fb-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="294fb-374">Vlastnosti s `[ViewData]` atributem mají své hodnoty uložené a načtené z <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="294fb-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="294fb-375">V následujícím příkladu `AboutModel` použije `[ViewData]` atribut na `Title` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="294fb-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="294fb-376">Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="294fb-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="294fb-377">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="294fb-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="294fb-378">TempData</span><span class="sxs-lookup"><span data-stu-id="294fb-378">TempData</span></span>

<span data-ttu-id="294fb-379">ASP.NET Core zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="294fb-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="294fb-380">Tato vlastnost ukládá data do jejich čtení.</span><span class="sxs-lookup"><span data-stu-id="294fb-380">This property stores data until it's read.</span></span> <span data-ttu-id="294fb-381"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*>Metody a <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> lze použít k prohlédnutí dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="294fb-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="294fb-382">`TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="294fb-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="294fb-383">Následující kód nastaví hodnotu `Message` pomocí `TempData` :</span><span class="sxs-lookup"><span data-stu-id="294fb-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="294fb-384">Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="294fb-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="294fb-385">Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="294fb-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="294fb-386">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="294fb-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="294fb-387">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="294fb-387">Multiple handlers per page</span></span>

<span data-ttu-id="294fb-388">Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="294fb-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="294fb-389">Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` pro odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="294fb-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="294fb-390">`asp-page-handler`Atribut je doprovodný objekt k `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="294fb-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="294fb-391">`asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou.</span><span class="sxs-lookup"><span data-stu-id="294fb-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="294fb-392">`asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="294fb-393">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="294fb-393">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="294fb-394">Předchozí kód používá *pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="294fb-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="294fb-395">Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="294fb-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="294fb-396">V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="294fb-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="294fb-397">Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="294fb-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="294fb-398">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="294fb-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="294fb-399">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="294fb-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="294fb-400">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="294fb-400">Custom routes</span></span>

<span data-ttu-id="294fb-401">Použijte tuto `@page` direktivu pro:</span><span class="sxs-lookup"><span data-stu-id="294fb-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="294fb-402">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-402">Specify a custom route to a page.</span></span> <span data-ttu-id="294fb-403">Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="294fb-404">Připojit segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-404">Append segments to a page's default route.</span></span> <span data-ttu-id="294fb-405">Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="294fb-406">Připojení parametrů k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="294fb-407">Například parametr ID `id` může být požadován pro stránku s `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="294fb-408">Je podporována kořenová relativní cesta, která je určena vlnovkou ( `~` ) na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="294fb-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="294fb-409">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="294fb-410">Pokud se v adrese URL nelíbí řetězec dotazu `?handler=JoinList` , změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="294fb-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="294fb-411">Trasa se dá přizpůsobit přidáním šablony trasy, která je uzavřená do dvojitých uvozovek za `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="294fb-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="294fb-412">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="294fb-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="294fb-413">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="294fb-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="294fb-414">`?`Následující `handler` údaj znamená, že parametr trasy je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="294fb-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="294fb-415">Pokročilá konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="294fb-415">Advanced configuration and settings</span></span>

<span data-ttu-id="294fb-416">Konfigurace a nastavení v následujících oddílech není pro většinu aplikací vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="294fb-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="294fb-417">Pro konfiguraci pokročilých možností použijte <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> přetížení, které konfiguruje <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="294fb-417">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="294fb-418">Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> můžete nastavit kořenový adresář pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="294fb-419">Další informace o konvencích najdete v tématu [ Razor autorizační konvence stránek](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="294fb-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="294fb-420">Chcete-li předkompilovat zobrazení, přečtěte si téma [ Razor zobrazení kompilace](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="294fb-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="294fb-421">Zadejte, jestli Razor se stránky nacházejí v kořenu obsahu.</span><span class="sxs-lookup"><span data-stu-id="294fb-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="294fb-422">Ve výchozím nastavení Razor se stránky rootují v adresáři */Pages* .</span><span class="sxs-lookup"><span data-stu-id="294fb-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="294fb-423">Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> k určení, že vaše Razor stránky jsou v [kořenu obsahu](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) aplikace:</span><span class="sxs-lookup"><span data-stu-id="294fb-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="294fb-424">Zadejte, jestli Razor jsou stránky ve vlastním kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="294fb-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="294fb-425">Přidat <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> k určení, že Razor stránky jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="294fb-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="294fb-426">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="294fb-426">Additional resources</span></span>

* <span data-ttu-id="294fb-427">Viz Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start), které jsou založeny na tomto úvodu.</span><span class="sxs-lookup"><span data-stu-id="294fb-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="294fb-428">Autorizovat atributy a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="294fb-428">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="294fb-429">Stáhnout nebo zobrazit ukázkový kód</span><span class="sxs-lookup"><span data-stu-id="294fb-429">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
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

<span data-ttu-id="294fb-430">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="294fb-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="294fb-431">RazorStránky jsou novým aspektem ASP.NET Core MVC, který usnadňuje a produktivnější vytváření kódu pro scénáře zaměřené na stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="294fb-432">Pokud hledáte kurz, který používá přístup k modelovým zobrazením, přečtěte si téma Začínáme [s ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="294fb-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="294fb-433">Tento dokument popisuje úvodní Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="294fb-434">Nejedná se o podrobný kurz.</span><span class="sxs-lookup"><span data-stu-id="294fb-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="294fb-435">Pokud zjistíte, že některé části jsou moc pokročilé, přečtěte si téma Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="294fb-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="294fb-436">Přehled ASP.NET Core najdete v [úvodu k ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="294fb-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="294fb-437">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="294fb-437">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="294fb-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="294fb-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="294fb-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="294fb-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="294fb-440">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="294fb-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="294fb-441">Vytvořit Razor projekt stránek</span><span class="sxs-lookup"><span data-stu-id="294fb-441">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="294fb-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="294fb-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="294fb-443">Podrobné pokyny k vytvoření projektu stránky najdete v tématu Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="294fb-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="294fb-444">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="294fb-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="294fb-445">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="294fb-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="294fb-446">Otevřete vygenerovaný soubor *. csproj* z Visual Studio pro Mac.</span><span class="sxs-lookup"><span data-stu-id="294fb-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="294fb-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="294fb-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="294fb-448">Spusťte `dotnet new webapp` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="294fb-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="294fb-449">RazorStránky</span><span class="sxs-lookup"><span data-stu-id="294fb-449">Razor Pages</span></span>

<span data-ttu-id="294fb-450">RazorStránky jsou povolené v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="294fb-450">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="294fb-451">Zvažte základní stránku:<a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="294fb-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="294fb-452">Předchozí kód vypadá jako [ Razor soubor zobrazení](xref:tutorials/first-mvc-app/adding-view) používaný v aplikaci ASP.NET Core s řadiči a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="294fb-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="294fb-453">To znamená, že tato direktiva je odlišná `@page` .</span><span class="sxs-lookup"><span data-stu-id="294fb-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="294fb-454">`@page`Vytvoří soubor na akci MVC – to znamená, že zpracovává požadavky přímo, bez přechodu přes kontroler.</span><span class="sxs-lookup"><span data-stu-id="294fb-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="294fb-455">`@page`musí se jednat o první Razor direktivu na stránce.</span><span class="sxs-lookup"><span data-stu-id="294fb-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="294fb-456">`@page`má vliv na chování jiných Razor konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="294fb-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="294fb-457">Podobná stránka, která používá `PageModel` třídu, je zobrazena v následujících dvou souborech.</span><span class="sxs-lookup"><span data-stu-id="294fb-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="294fb-458">Soubor *Pages/Index2. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="294fb-459">Model stránky *stránky/Index2. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="294fb-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="294fb-460">Podle konvence `PageModel` má soubor třídy stejný název jako Razor stránkovací soubor s *příponou. cs* .</span><span class="sxs-lookup"><span data-stu-id="294fb-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="294fb-461">Například předchozí Razor stránka je *Pages/Index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="294fb-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="294fb-462">Soubor obsahující `PageModel` třídu má název *Pages/Index2. cshtml. cs*.</span><span class="sxs-lookup"><span data-stu-id="294fb-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="294fb-463">Přidružení cest URL ke stránkám závisí na umístění stránky v systému souborů.</span><span class="sxs-lookup"><span data-stu-id="294fb-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="294fb-464">Následující tabulka ukazuje Razor cestu stránky a adresu URL pro porovnání:</span><span class="sxs-lookup"><span data-stu-id="294fb-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="294fb-465">Název souboru a cesta</span><span class="sxs-lookup"><span data-stu-id="294fb-465">File name and path</span></span>               | <span data-ttu-id="294fb-466">shodná adresa URL</span><span class="sxs-lookup"><span data-stu-id="294fb-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="294fb-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="294fb-468">`/` nebo `/Index`</span><span class="sxs-lookup"><span data-stu-id="294fb-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="294fb-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="294fb-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="294fb-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="294fb-472">`/Store` nebo `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="294fb-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="294fb-473">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="294fb-473">Notes:</span></span>

* <span data-ttu-id="294fb-474">Modul runtime Razor ve výchozím nastavení vyhledá soubory stránek ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="294fb-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="294fb-475">`Index`je výchozí stránka, když adresa URL neobsahuje stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="294fb-476">Napsat základní formulář</span><span class="sxs-lookup"><span data-stu-id="294fb-476">Write a basic form</span></span>

<span data-ttu-id="294fb-477">RazorStránky jsou navržené tak, aby při vytváření aplikace byly běžné vzory používané s webovými prohlížeči, které se dají snadno implementovat.</span><span class="sxs-lookup"><span data-stu-id="294fb-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="294fb-478">[Vazba modelů](xref:mvc/models/model-binding), [pomocníky značek](xref:mvc/views/tag-helpers/intro)a HTML pomocníků pro HTML *stačí pracovat* s vlastnostmi definovanými ve Razor třídě stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="294fb-479">Zvažte stránku, která pro model implementuje základní formulář "kontaktujte nás" `Contact` :</span><span class="sxs-lookup"><span data-stu-id="294fb-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="294fb-480">Pro ukázky v tomto dokumentu `DbContext` se inicializuje v souboru [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) .</span><span class="sxs-lookup"><span data-stu-id="294fb-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="294fb-481">Datový model:</span><span class="sxs-lookup"><span data-stu-id="294fb-481">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="294fb-482">Kontext databáze:</span><span class="sxs-lookup"><span data-stu-id="294fb-482">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="294fb-483">Soubor zobrazení *Pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="294fb-484">Model stránky *Pages/Create. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="294fb-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="294fb-485">Podle konvence `PageModel` je třída volána `<PageName>Model` a je ve stejném oboru názvů jako stránka.</span><span class="sxs-lookup"><span data-stu-id="294fb-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="294fb-486">`PageModel`Třída umožňuje oddělení logiky stránky od její prezentace.</span><span class="sxs-lookup"><span data-stu-id="294fb-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="294fb-487">Definuje obslužné rutiny stránky pro požadavky odeslané na stránku a data použitá k vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="294fb-488">Toto oddělení umožňuje:</span><span class="sxs-lookup"><span data-stu-id="294fb-488">This separation allows:</span></span>

* <span data-ttu-id="294fb-489">Správa závislostí stránky prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="294fb-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="294fb-490">[Testování částí](xref:test/razor-pages-tests) stránky</span><span class="sxs-lookup"><span data-stu-id="294fb-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="294fb-491">Stránka obsahuje `OnPostAsync` *metodu obslužné rutiny*, která běží na `POST` žádostech (když uživatel formulář odešle).</span><span class="sxs-lookup"><span data-stu-id="294fb-491">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="294fb-492">Můžete přidat metody obslužné rutiny pro jakýkoli příkaz HTTP.</span><span class="sxs-lookup"><span data-stu-id="294fb-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="294fb-493">Nejběžnější obslužné rutiny jsou:</span><span class="sxs-lookup"><span data-stu-id="294fb-493">The most common handlers are:</span></span>

* <span data-ttu-id="294fb-494">`OnGet` pro inicializaci stavu potřebného pro stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="294fb-495">Ukázka [OnGet](#OnGet)</span><span class="sxs-lookup"><span data-stu-id="294fb-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="294fb-496">`OnPost` pro zpracování odesílání formulářů.</span><span class="sxs-lookup"><span data-stu-id="294fb-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="294fb-497">Přípona názvu `Async` je volitelná, ale často se podle konvence používá pro asynchronní funkce.</span><span class="sxs-lookup"><span data-stu-id="294fb-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="294fb-498">Předchozí kód je typický pro Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="294fb-499">Pokud jste obeznámeni s aplikacemi ASP.NET pomocí řadičů a zobrazení:</span><span class="sxs-lookup"><span data-stu-id="294fb-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="294fb-500">`OnPostAsync`Kód v předchozím příkladu vypadá podobně jako typický kód kontroleru.</span><span class="sxs-lookup"><span data-stu-id="294fb-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="294fb-501">Většina primitivních prvků MVC, jako je [vazba modelů](xref:mvc/models/model-binding), [ověřování](xref:mvc/models/validation), [ověřování](xref:mvc/models/validation)a výsledky akce, jsou sdílené.</span><span class="sxs-lookup"><span data-stu-id="294fb-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="294fb-502">Předchozí `OnPostAsync` metoda:</span><span class="sxs-lookup"><span data-stu-id="294fb-502">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="294fb-503">Základní tok `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="294fb-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="294fb-504">Kontrola chyb ověřování.</span><span class="sxs-lookup"><span data-stu-id="294fb-504">Check for validation errors.</span></span>

* <span data-ttu-id="294fb-505">V případě, že nejsou k dispozici žádné chyby, uložte data a přesměrujte je.</span><span class="sxs-lookup"><span data-stu-id="294fb-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="294fb-506">Pokud dojde k chybám, zobrazte stránku znovu s ověřovacími zprávami.</span><span class="sxs-lookup"><span data-stu-id="294fb-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="294fb-507">Ověřování na straně klienta je stejné jako tradiční aplikace ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="294fb-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="294fb-508">V mnoha případech by se v klientovi zjistily chyby ověřování a nikdy by se neodeslaly na server.</span><span class="sxs-lookup"><span data-stu-id="294fb-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="294fb-509">Po úspěšném zadání dat `OnPostAsync` zavolá metoda obslužné rutiny `RedirectToPage` pomocnou metodu, která vrátí instanci `RedirectToPageResult` .</span><span class="sxs-lookup"><span data-stu-id="294fb-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="294fb-510">`RedirectToPage`je nový výsledek akce, který se podobá `RedirectToAction` nebo `RedirectToRoute` , ale přizpůsobený pro stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="294fb-511">V předchozí ukázce přesměruje na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="294fb-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="294fb-512">`RedirectToPage`je podrobně popsán v části [generování adresy URL pro stránky](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="294fb-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="294fb-513">Když odeslaný formulář obsahuje chyby ověřování (které jsou předány serveru), `OnPostAsync` Metoda obslužné rutiny volá `Page` pomocnou metodu.</span><span class="sxs-lookup"><span data-stu-id="294fb-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="294fb-514">`Page` vrací instanci `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="294fb-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="294fb-515">Vrácení `Page` se podobá tomu, jak vrátí akce v řadičích `View` .</span><span class="sxs-lookup"><span data-stu-id="294fb-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="294fb-516">`PageResult`je výchozí návratový typ pro metodu obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="294fb-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="294fb-517">Metoda obslužné rutiny, která vrací `void` vykreslení stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="294fb-518">`Customer`Vlastnost používá `[BindProperty]` atribut pro přihlášení k vazbě modelu.</span><span class="sxs-lookup"><span data-stu-id="294fb-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="294fb-519">RazorStránky, ve výchozím nastavení vlastnosti BIND pouze bez `GET` slovesa.</span><span class="sxs-lookup"><span data-stu-id="294fb-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="294fb-520">Vazba na vlastnosti může snížit množství kódu, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="294fb-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="294fb-521">Vazba zkracuje kód pomocí stejné vlastnosti pro vykreslení polí formuláře ( `<input asp-for="Customer.Name">` ) a přijměte vstup.</span><span class="sxs-lookup"><span data-stu-id="294fb-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="294fb-522">Domovská stránka (*index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="294fb-522">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="294fb-523">Přidružená `PageModel` třída (*index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="294fb-523">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="294fb-524">Soubor *index. cshtml* obsahuje následující kód pro vytvoření odkazu pro úpravy pro každý kontakt:</span><span class="sxs-lookup"><span data-stu-id="294fb-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="294fb-525">`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Pomocná značka značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) použila `asp-route-{value}` atribut k vygenerování odkazu na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="294fb-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="294fb-526">Odkaz obsahuje data směrování s ID kontaktu.</span><span class="sxs-lookup"><span data-stu-id="294fb-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="294fb-527">Například, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="294fb-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="294fb-528">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="294fb-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="294fb-529">Pomocník značek je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="294fb-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="294fb-530">Soubor *Pages/Edit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="294fb-531">První řádek obsahuje `@page "{id:int}"` direktivu.</span><span class="sxs-lookup"><span data-stu-id="294fb-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="294fb-532">Omezení směrování `"{id:int}"` instruuje stránku, aby přijímala požadavky na stránku, která obsahuje `int` data směrování.</span><span class="sxs-lookup"><span data-stu-id="294fb-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="294fb-533">Pokud požadavek na stránku neobsahuje směrovací data, která je možné převést na `int` , modul runtime vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="294fb-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="294fb-534">Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="294fb-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="294fb-535">Soubor *Pages/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="294fb-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="294fb-536">Soubor *index. cshtml* také obsahuje značky pro vytvoření tlačítka Odstranit pro kontaktování každého zákazníka:</span><span class="sxs-lookup"><span data-stu-id="294fb-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="294fb-537">Když je tlačítko Odstranit vykresleno ve formátu HTML, `formaction` obsahuje parametry pro:</span><span class="sxs-lookup"><span data-stu-id="294fb-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="294fb-538">ID kontaktu zákazníka určené `asp-route-id` atributem</span><span class="sxs-lookup"><span data-stu-id="294fb-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="294fb-539">`handler`Zadané `asp-page-handler` atributem.</span><span class="sxs-lookup"><span data-stu-id="294fb-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="294fb-540">Tady je příklad vygenerovaného tlačítka odstranit s ID kontaktu zákazníka `1` :</span><span class="sxs-lookup"><span data-stu-id="294fb-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="294fb-541">Když je vybráno tlačítko, `POST` pošle se na server požadavek na formulář.</span><span class="sxs-lookup"><span data-stu-id="294fb-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="294fb-542">Podle konvence je název metody obslužné rutiny vybraný na základě hodnoty `handler` parametru podle schématu `OnPost[handler]Async` .</span><span class="sxs-lookup"><span data-stu-id="294fb-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="294fb-543">Vzhledem k `handler` `delete` tomu, že je v tomto příkladu, `OnPostDeleteAsync` Metoda obslužné rutiny se používá ke zpracování `POST` požadavku.</span><span class="sxs-lookup"><span data-stu-id="294fb-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="294fb-544">Pokud `asp-page-handler` je nastaven na jinou hodnotu, například `remove` , je vybrána metoda obslužné rutiny s názvem `OnPostRemoveAsync` .</span><span class="sxs-lookup"><span data-stu-id="294fb-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="294fb-545">Následující kód ukazuje `OnPostDeleteAsync` obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="294fb-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="294fb-546">`OnPostDeleteAsync`Metoda:</span><span class="sxs-lookup"><span data-stu-id="294fb-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="294fb-547">Akceptuje `id` z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="294fb-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="294fb-548">Pokud direktiva stránky *index. cshtml* obsahuje omezení směrování `"{id:int?}"` , `id` pocházela z dat směrování.</span><span class="sxs-lookup"><span data-stu-id="294fb-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="294fb-549">Data trasy pro `id` se zadává v identifikátoru URI, jako je například `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="294fb-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="294fb-550">Dotazuje databázi na kontaktování zákazníka `FindAsync` .</span><span class="sxs-lookup"><span data-stu-id="294fb-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="294fb-551">Pokud se kontakt zákazníka najde, odebere se ze seznamu kontaktů zákazníka.</span><span class="sxs-lookup"><span data-stu-id="294fb-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="294fb-552">Databáze je aktualizována.</span><span class="sxs-lookup"><span data-stu-id="294fb-552">The database is updated.</span></span>
* <span data-ttu-id="294fb-553">Volání `RedirectToPage` pro přesměrování na stránku kořenového indexu ( `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="294fb-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="294fb-554">Označit vlastnosti stránky jako povinné</span><span class="sxs-lookup"><span data-stu-id="294fb-554">Mark page properties as required</span></span>

<span data-ttu-id="294fb-555">Vlastnosti v `PageModel` lze označit [požadovaným](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) atributem:</span><span class="sxs-lookup"><span data-stu-id="294fb-555">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="294fb-556">Další informace najdete v tématu [ověřování modelu](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="294fb-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="294fb-557">Zpracování požadavků HEAD pomocí Fallback obslužné rutiny OnGet</span><span class="sxs-lookup"><span data-stu-id="294fb-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="294fb-558">`HEAD`žádosti umožňují načíst hlavičky pro konkrétní prostředek.</span><span class="sxs-lookup"><span data-stu-id="294fb-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="294fb-559">Na rozdíl od `GET` požadavků `HEAD` požadavky nevrátí tělo odpovědi.</span><span class="sxs-lookup"><span data-stu-id="294fb-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="294fb-560">Obvykle `OnHead` je obslužná rutina vytvořena a volána pro `HEAD` požadavky:</span><span class="sxs-lookup"><span data-stu-id="294fb-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="294fb-561">V ASP.NET Core 2,1 nebo novějších se Razor stránky vrátí k volání `OnGet` obslužné rutiny, pokud `OnHead` není definována žádná obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="294fb-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="294fb-562">Toto chování je povoleno voláním [SetCompatibilityVersion](xref:mvc/compatibility-version) v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="294fb-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="294fb-563">Výchozí šablony generují `SetCompatibilityVersion` volání v ASP.NET Core 2,1 a 2,2.</span><span class="sxs-lookup"><span data-stu-id="294fb-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="294fb-564">`SetCompatibilityVersion`efektivně nastaví Razor možnost stránky `AllowMappingHeadRequestsToGetHandler` na `true` .</span><span class="sxs-lookup"><span data-stu-id="294fb-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="294fb-565">Místo toho, abyste se rozhodli pro všechna chování pomocí `SetCompatibilityVersion` , se můžete výslovně vyjádřit ke *konkrétnímu* chování.</span><span class="sxs-lookup"><span data-stu-id="294fb-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="294fb-566">Následující kód výslovný v pro povolení `HEAD` Mapování požadavků na `OnGet` obslužnou rutinu:</span><span class="sxs-lookup"><span data-stu-id="294fb-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="294fb-567">XSRF/CSRF a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="294fb-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="294fb-568">Nemusíte psát žádný kód pro [ověřování proti padělání](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="294fb-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="294fb-569">Generování a ověření tokenu antipadělání jsou automaticky zahrnuty na Razor stránkách.</span><span class="sxs-lookup"><span data-stu-id="294fb-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="294fb-570">Použití rozložení, částečných, šablon a značek pomocníka se Razor stránkami</span><span class="sxs-lookup"><span data-stu-id="294fb-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="294fb-571">Stránky fungují se všemi možnostmi Razor modulu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="294fb-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="294fb-572">Rozložení, částečné typy, šablony, pomocníka značek, *_ViewStart. cshtml*, *_ViewImports. cshtml* fungují stejným způsobem jako u konvenčních Razor zobrazení.</span><span class="sxs-lookup"><span data-stu-id="294fb-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="294fb-573">Pojďme tuto stránku obstarit tím, že využijete některé z těchto možností.</span><span class="sxs-lookup"><span data-stu-id="294fb-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="294fb-574">Přidejte [stránku rozložení](xref:mvc/views/layout) na *stránky/sdílené/_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="294fb-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="294fb-575">[Rozložení](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="294fb-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="294fb-576">Určuje rozložení jednotlivých stránek (Pokud stránka výslovný mimo rozložení).</span><span class="sxs-lookup"><span data-stu-id="294fb-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="294fb-577">Importuje struktury HTML, jako jsou JavaScript a StyleSheet.</span><span class="sxs-lookup"><span data-stu-id="294fb-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="294fb-578">Další informace najdete na [stránce rozložení](xref:mvc/views/layout) .</span><span class="sxs-lookup"><span data-stu-id="294fb-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="294fb-579">Vlastnost [layout](xref:mvc/views/layout#specifying-a-layout) je nastavena na *stránky/_ViewStart. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="294fb-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="294fb-580">Rozložení se nachází na *stránkách nebo ve sdílené* složce.</span><span class="sxs-lookup"><span data-stu-id="294fb-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="294fb-581">Stránky hledají další zobrazení (rozložení, šablony, částečné typy) hierarchicky a začínají ve stejné složce jako aktuální stránka.</span><span class="sxs-lookup"><span data-stu-id="294fb-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="294fb-582">Rozložení na *stránkách nebo ve sdílené* složce můžete použít na libovolné Razor stránce ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="294fb-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="294fb-583">Soubor rozložení by měl přejít na *stránky nebo do sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="294fb-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="294fb-584">Nedoporučujeme **umístit soubor** rozložení do *zobrazení/sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="294fb-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="294fb-585">*Zobrazení/Shared* je vzor zobrazení MVC.</span><span class="sxs-lookup"><span data-stu-id="294fb-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="294fb-586">RazorStránky mají sloužit k spoléhání se na hierarchii složek, nikoli na konvence cest.</span><span class="sxs-lookup"><span data-stu-id="294fb-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="294fb-587">Zobrazení hledání ze Razor stránky obsahuje složku *stránky* .</span><span class="sxs-lookup"><span data-stu-id="294fb-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="294fb-588">Rozložení, šablony a částečné typy, které používáte s řadiči MVC a konvenčními Razor zobrazeními, *fungují pouze*.</span><span class="sxs-lookup"><span data-stu-id="294fb-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="294fb-589">Přidejte soubor *Pages/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="294fb-590">`@namespace`je vysvětleno dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="294fb-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="294fb-591">Tato `@addTagHelper` direktiva přináší [předdefinované pomocníky značek](xref:mvc/views/tag-helpers/builtin-th/Index) všem stránkám ve složce *Pages* .</span><span class="sxs-lookup"><span data-stu-id="294fb-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="294fb-592">V případě `@namespace` explicitního použití direktivy na stránce:</span><span class="sxs-lookup"><span data-stu-id="294fb-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="294fb-593">Direktiva nastaví obor názvů pro stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="294fb-594">`@model`Direktiva nemusí zahrnovat obor názvů.</span><span class="sxs-lookup"><span data-stu-id="294fb-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="294fb-595">Pokud `@namespace` je direktiva obsažena v *_ViewImports. cshtml*, zadaný obor názvů poskytuje předponu pro generovaný obor názvů na stránce, která importuje `@namespace` direktivu.</span><span class="sxs-lookup"><span data-stu-id="294fb-595">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="294fb-596">Zbytek generovaného oboru názvů (část přípony) je relativní cesta oddělená tečkou mezi složkou obsahující *_ViewImports. cshtml* a složkou obsahující stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="294fb-597">Například `PageModel` třídy *Pages/Customers/Edit. cshtml. cs* explicitně nastaví obor názvů:</span><span class="sxs-lookup"><span data-stu-id="294fb-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="294fb-598">Soubor *Pages/_ViewImports. cshtml* nastaví následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="294fb-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="294fb-599">Vygenerovaný obor názvů pro stránku *Pages/Customers/Edit. cshtml* Razor je stejný jako `PageModel` Třída.</span><span class="sxs-lookup"><span data-stu-id="294fb-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="294fb-600">`@namespace`*funguje také s konvenčními Razor zobrazeními.*</span><span class="sxs-lookup"><span data-stu-id="294fb-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="294fb-601">Soubor zobrazení původní *stránky/vytvořit. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="294fb-602">Aktualizovaný soubor zobrazení *stránky/vytvoření. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="294fb-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="294fb-603">[ Razor Úvodní projekt stránky](#rpvs17) obsahuje *stránky/_ValidationScriptsPartial. cshtml*, které se připojí k ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="294fb-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="294fb-604">Další informace o částečných zobrazeních naleznete v tématu <xref:mvc/views/partial> .</span><span class="sxs-lookup"><span data-stu-id="294fb-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="294fb-605">Generování adresy URL pro stránky</span><span class="sxs-lookup"><span data-stu-id="294fb-605">URL generation for Pages</span></span>

<span data-ttu-id="294fb-606">`Create`Stránka, která se zobrazuje dřív, používá `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="294fb-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="294fb-607">Aplikace má následující strukturu souborů nebo složek:</span><span class="sxs-lookup"><span data-stu-id="294fb-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="294fb-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="294fb-608">*/Pages*</span></span>

  * <span data-ttu-id="294fb-609">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="294fb-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="294fb-610">*/Customers*</span></span>

    * <span data-ttu-id="294fb-611">*Vytvořit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="294fb-612">*Upravit. cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="294fb-613">*Soubor Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="294fb-613">*Index.cshtml*</span></span>

<span data-ttu-id="294fb-614">*Stránky/zákazníci/vytvořit. cshtml* a *Pages/Customers/Edit.* cshtml Pages přesměruje na *pages/index. cshtml* po úspěchu.</span><span class="sxs-lookup"><span data-stu-id="294fb-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="294fb-615">Řetězec `/Index` je součástí identifikátoru URI pro přístup k předchozí stránce.</span><span class="sxs-lookup"><span data-stu-id="294fb-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="294fb-616">Řetězec `/Index` lze použít ke generování identifikátorů URI na stránce *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="294fb-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="294fb-617">Příklad:</span><span class="sxs-lookup"><span data-stu-id="294fb-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="294fb-618">Název stránky je cesta ke stránce z kořenové složky */Pages* , včetně úvodní `/` (například `/Index` ).</span><span class="sxs-lookup"><span data-stu-id="294fb-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="294fb-619">Předchozí ukázky generování adresy URL nabízejí rozšířené možnosti a funkce, které se zakódujeme na adresu URL.</span><span class="sxs-lookup"><span data-stu-id="294fb-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="294fb-620">Generování adresy URL používá [Směrování](xref:mvc/controllers/routing) a může vygenerovat a kódovat parametry podle toho, jak je trasa definována v cílové cestě.</span><span class="sxs-lookup"><span data-stu-id="294fb-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="294fb-621">Generování adresy URL pro stránky podporuje relativní názvy.</span><span class="sxs-lookup"><span data-stu-id="294fb-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="294fb-622">Následující tabulka ukazuje, která stránka indexu je vybrána s různými `RedirectToPage` parametry ze *stránky/zákazníci/vytvořit. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="294fb-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="294fb-623">RedirectToPage (x)</span><span class="sxs-lookup"><span data-stu-id="294fb-623">RedirectToPage(x)</span></span>| <span data-ttu-id="294fb-624">Stránka</span><span class="sxs-lookup"><span data-stu-id="294fb-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="294fb-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="294fb-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="294fb-626">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-626">*Pages/Index*</span></span> |
| <span data-ttu-id="294fb-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="294fb-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="294fb-628">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="294fb-629">RedirectToPage(".. /Index")</span><span class="sxs-lookup"><span data-stu-id="294fb-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="294fb-630">*Stránky/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-630">*Pages/Index*</span></span> |
| <span data-ttu-id="294fb-631">RedirectToPage ("index")</span><span class="sxs-lookup"><span data-stu-id="294fb-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="294fb-632">*Stránky/zákazníci/rejstřík*</span><span class="sxs-lookup"><span data-stu-id="294fb-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="294fb-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")` a `RedirectToPage("../Index")` jsou *relativní názvy*.</span><span class="sxs-lookup"><span data-stu-id="294fb-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="294fb-634">`RedirectToPage`Parametr je *kombinován* s cestou aktuální stránky k výpočtu názvu cílové stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="294fb-635">Propojení relativního názvu je užitečné při vytváření webů se složitou strukturou.</span><span class="sxs-lookup"><span data-stu-id="294fb-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="294fb-636">Pokud k propojení mezi stránkami ve složce použijete relativní názvy, můžete tuto složku přejmenovat.</span><span class="sxs-lookup"><span data-stu-id="294fb-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="294fb-637">Všechny odkazy pořád fungují (protože neobsahují název složky).</span><span class="sxs-lookup"><span data-stu-id="294fb-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="294fb-638">Chcete-li přesměrovat na stránku v jiné [oblasti](xref:mvc/controllers/areas), zadejte oblast:</span><span class="sxs-lookup"><span data-stu-id="294fb-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="294fb-639">Další informace naleznete v tématu <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="294fb-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="294fb-640">ViewData – atribut</span><span class="sxs-lookup"><span data-stu-id="294fb-640">ViewData attribute</span></span>

<span data-ttu-id="294fb-641">Data je možné předat na stránku pomocí [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="294fb-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="294fb-642">Vlastnosti v řadičích nebo Razor modelech stránek s `[ViewData]` atributem mají své hodnoty uložené a načtené z [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="294fb-642">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="294fb-643">V následujícím příkladu `AboutModel` obsahuje `Title` vlastnost s označením `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="294fb-643">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="294fb-644">`Title`Vlastnost je nastavena na název stránky o produktu:</span><span class="sxs-lookup"><span data-stu-id="294fb-644">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="294fb-645">Na stránce o aplikaci získáte přístup k `Title` vlastnosti jako vlastnost modelu:</span><span class="sxs-lookup"><span data-stu-id="294fb-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="294fb-646">V rozložení je název čten ze slovníku ViewData:</span><span class="sxs-lookup"><span data-stu-id="294fb-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="294fb-647">TempData</span><span class="sxs-lookup"><span data-stu-id="294fb-647">TempData</span></span>

<span data-ttu-id="294fb-648">ASP.NET Core zpřístupňuje vlastnost [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) na [řadiči](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="294fb-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="294fb-649">Tato vlastnost ukládá data do jejich čtení.</span><span class="sxs-lookup"><span data-stu-id="294fb-649">This property stores data until it's read.</span></span> <span data-ttu-id="294fb-650">`Keep`Metody a `Peek` lze použít k prohlédnutí dat bez odstranění.</span><span class="sxs-lookup"><span data-stu-id="294fb-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="294fb-651">`TempData`je vhodný pro přesměrování, pokud jsou potřebná data pro více než jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="294fb-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="294fb-652">Následující kód nastaví hodnotu `Message` pomocí `TempData` :</span><span class="sxs-lookup"><span data-stu-id="294fb-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="294fb-653">Následující kód v souboru *Pages/Customers/index. cshtml* zobrazuje hodnotu `Message` using `TempData` .</span><span class="sxs-lookup"><span data-stu-id="294fb-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="294fb-654">Model stránky *Pages/Customers/index. cshtml. cs* aplikuje `[TempData]` atribut na `Message` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="294fb-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="294fb-655">Další informace najdete v tématu [TempData](xref:fundamentals/app-state#tempdata) .</span><span class="sxs-lookup"><span data-stu-id="294fb-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="294fb-656">Více obslužných rutin na stránku</span><span class="sxs-lookup"><span data-stu-id="294fb-656">Multiple handlers per page</span></span>

<span data-ttu-id="294fb-657">Následující stránka generuje značky pro dvě obslužné rutiny pomocí `asp-page-handler` pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="294fb-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="294fb-658">Formulář v předchozím příkladu obsahuje dvě tlačítka pro odeslání, z nichž každá používá `FormActionTagHelper` pro odeslání na jinou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="294fb-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="294fb-659">`asp-page-handler`Atribut je doprovodný objekt k `asp-page` .</span><span class="sxs-lookup"><span data-stu-id="294fb-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="294fb-660">`asp-page-handler`generuje adresy URL, které odesílají do každé z metod obslužné rutiny, které jsou definovány stránkou.</span><span class="sxs-lookup"><span data-stu-id="294fb-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="294fb-661">`asp-page`není zadáno, protože ukázka odkazuje na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="294fb-662">Model stránky:</span><span class="sxs-lookup"><span data-stu-id="294fb-662">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="294fb-663">Předchozí kód používá *pojmenované obslužné rutiny*.</span><span class="sxs-lookup"><span data-stu-id="294fb-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="294fb-664">Pojmenované obslužné rutiny jsou vytvořeny pomocí textu v názvu po `On<HTTP Verb>` a před `Async` (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="294fb-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="294fb-665">V předchozím příkladu jsou metody stránky**JoinList**Async a post**JoinListUC**Async.</span><span class="sxs-lookup"><span data-stu-id="294fb-665">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="294fb-666">Když jsou rutiny *post* a *Async* odebrány, názvy obslužných rutin jsou `JoinList` a `JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="294fb-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="294fb-667">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinList` .</span><span class="sxs-lookup"><span data-stu-id="294fb-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="294fb-668">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="294fb-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="294fb-669">Vlastní trasy</span><span class="sxs-lookup"><span data-stu-id="294fb-669">Custom routes</span></span>

<span data-ttu-id="294fb-670">Použijte tuto `@page` direktivu pro:</span><span class="sxs-lookup"><span data-stu-id="294fb-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="294fb-671">Zadejte vlastní trasu na stránku.</span><span class="sxs-lookup"><span data-stu-id="294fb-671">Specify a custom route to a page.</span></span> <span data-ttu-id="294fb-672">Například trasa na stránku o aplikaci může být nastavena na `/Some/Other/Path` `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="294fb-673">Připojit segmenty k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-673">Append segments to a page's default route.</span></span> <span data-ttu-id="294fb-674">Například segment "položka" lze přidat do výchozí trasy stránky s `@page "item"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="294fb-675">Připojení parametrů k výchozí trase stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="294fb-676">Například parametr ID `id` může být požadován pro stránku s `@page "{id}"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="294fb-677">Je podporována kořenová relativní cesta, která je určena vlnovkou ( `~` ) na začátku cesty.</span><span class="sxs-lookup"><span data-stu-id="294fb-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="294fb-678">Například `@page "~/Some/Other/Path"` je stejný jako `@page "/Some/Other/Path"` .</span><span class="sxs-lookup"><span data-stu-id="294fb-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="294fb-679">Pokud se v adrese URL nelíbí řetězec dotazu `?handler=JoinList` , změňte trasu tak, aby se název obslužné rutiny umístil do části cesty adresy URL.</span><span class="sxs-lookup"><span data-stu-id="294fb-679">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="294fb-680">Trasa se dá přizpůsobit přidáním šablony trasy, která je uzavřená do dvojitých uvozovek za `@page` direktivou.</span><span class="sxs-lookup"><span data-stu-id="294fb-680">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="294fb-681">Pomocí předchozího kódu je cesta URL, na kterou se odesílá, `OnPostJoinListAsync` `https://localhost:5001/Customers/CreateFATH/JoinList` .</span><span class="sxs-lookup"><span data-stu-id="294fb-681">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="294fb-682">Cesta URL, na kterou se odesílá `OnPostJoinListUCAsync` `https://localhost:5001/Customers/CreateFATH/JoinListUC` .</span><span class="sxs-lookup"><span data-stu-id="294fb-682">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="294fb-683">`?`Následující `handler` údaj znamená, že parametr trasy je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="294fb-683">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="294fb-684">Konfigurace a nastavení</span><span class="sxs-lookup"><span data-stu-id="294fb-684">Configuration and settings</span></span>

<span data-ttu-id="294fb-685">Pokud chcete konfigurovat pokročilé možnosti, použijte metodu rozšíření `AddRazorPagesOptions` v Tvůrci MVC:</span><span class="sxs-lookup"><span data-stu-id="294fb-685">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="294fb-686">V současné době můžete použít `RazorPagesOptions` k nastavení kořenového adresáře pro stránky nebo přidat konvence modelu aplikace pro stránky.</span><span class="sxs-lookup"><span data-stu-id="294fb-686">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="294fb-687">Tímto způsobem v budoucnu povolíme lepší rozšíření.</span><span class="sxs-lookup"><span data-stu-id="294fb-687">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="294fb-688">Chcete-li předkompilovat zobrazení, přečtěte si téma [ Razor zobrazení kompilace](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="294fb-688">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="294fb-689">[Stažení nebo zobrazení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="294fb-689">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="294fb-690">Viz Začínáme [se Razor stránkami](xref:tutorials/razor-pages/razor-pages-start), které jsou založeny na tomto úvodu.</span><span class="sxs-lookup"><span data-stu-id="294fb-690">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="294fb-691">Zadejte, jestli Razor se stránky nacházejí v kořenu obsahu.</span><span class="sxs-lookup"><span data-stu-id="294fb-691">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="294fb-692">Ve výchozím nastavení Razor se stránky rootují v adresáři */Pages* .</span><span class="sxs-lookup"><span data-stu-id="294fb-692">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="294fb-693">Přidejte [s Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete, jestli Razor jsou vaše stránky v [kořenu obsahu](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) aplikace:</span><span class="sxs-lookup"><span data-stu-id="294fb-693">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="294fb-694">Zadejte, jestli Razor jsou stránky ve vlastním kořenovém adresáři.</span><span class="sxs-lookup"><span data-stu-id="294fb-694">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="294fb-695">Přidejte [s Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) do [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) a určete tak, že vaše Razor stránky jsou v aplikaci vlastním kořenovým adresářem (zadejte relativní cestu):</span><span class="sxs-lookup"><span data-stu-id="294fb-695">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="294fb-696">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="294fb-696">Additional resources</span></span>

* [<span data-ttu-id="294fb-697">Autorizovat atributy a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="294fb-697">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
