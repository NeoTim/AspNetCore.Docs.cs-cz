---
title: Přidání vyhledávání do ASP.NET Core Razor Pages
author: rick-anderson
description: Ukazuje, jak přidat vyhledávací technologie ASP.NET Core Razor Pages
ms.author: riande
ms.date: 12/3/2018
uid: tutorials/razor-pages/search
ms.openlocfilehash: 3900b33f31fef79327d01b0579208355b0bce90c
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410216"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="73c57-103">Přidání vyhledávání do ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="73c57-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="73c57-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="73c57-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="73c57-105">V následujících částech hledání filmy podle *žánr* nebo *název* je přidána.</span><span class="sxs-lookup"><span data-stu-id="73c57-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="73c57-106">Přidejte následující zvýrazněný vlastnosti do *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="73c57-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="73c57-107">`SearchString`: obsahuje uživatele zadejte do textového pole hledání text.</span><span class="sxs-lookup"><span data-stu-id="73c57-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="73c57-108">`SearchString` je upravena pomocí [ `[BindProperty]` ](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="73c57-108">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="73c57-109">`[BindProperty]` vytvoří vazbu hodnot formuláře a řetězce dotazu se stejným názvem jako vlastnost.</span><span class="sxs-lookup"><span data-stu-id="73c57-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="73c57-110">`(SupportsGet = true)` je potřebná pro svázání pro požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="73c57-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="73c57-111">`Genres`: obsahuje seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="73c57-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="73c57-112">`Genres` Umožňuje uživateli vybrat rozšířením podle tematických ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="73c57-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="73c57-113">`SelectList` vyžaduje `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="73c57-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="73c57-114">`MovieGenre`: obsahuje konkrétní žánr vybere uživatele (například "západní").</span><span class="sxs-lookup"><span data-stu-id="73c57-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="73c57-115">`Genres` a `MovieGenre` později v tomto kurzu se používá.</span><span class="sxs-lookup"><span data-stu-id="73c57-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="73c57-116">Aktualizovat indexovou stránku `OnGetAsync` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="73c57-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="73c57-117">První řádek `OnGetAsync` metoda vytvoří [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) dotaz pro výběr videa:</span><span class="sxs-lookup"><span data-stu-id="73c57-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="73c57-118">Dotaz je *pouze* definované v tomto okamžiku, má **není** byly spuštěny v rámci databáze.</span><span class="sxs-lookup"><span data-stu-id="73c57-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="73c57-119">Pokud `SearchString` vlastnost není null nebo je prázdný, je filmy dotaz upravit tak, aby filtrování hledaný řetězec:</span><span class="sxs-lookup"><span data-stu-id="73c57-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="73c57-120">`s => s.Title.Contains()` Kód je [výraz Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="73c57-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="73c57-121">Výrazy lambda se používají v založených na volání metody [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) dotazuje jako argumenty pro standardní metody operátoru dotazu, jako [kde](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metoda nebo `Contains` (používá se v předchozím kódu).</span><span class="sxs-lookup"><span data-stu-id="73c57-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="73c57-122">Dotazy LINQ nejsou provedeny, když máte definovány, nebo data jejich voláním metody (například `Where`, `Contains` nebo `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="73c57-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="73c57-123">Místo toho provádění dotazu je odloženo.</span><span class="sxs-lookup"><span data-stu-id="73c57-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="73c57-124">To znamená, že vyhodnocení výrazu je zpožděna, dokud není procházen jeho očekávané hodnoty nebo `ToListAsync` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="73c57-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="73c57-125">Zobrazit [provádění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) Další informace.</span><span class="sxs-lookup"><span data-stu-id="73c57-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="73c57-126">**Poznámka:** [Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) spuštění metody je v databázi, není C# kódu.</span><span class="sxs-lookup"><span data-stu-id="73c57-126">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="73c57-127">Rozlišování velikosti písmen u dotazu, závisí na databázi a kolace.</span><span class="sxs-lookup"><span data-stu-id="73c57-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="73c57-128">Na serveru SQL Server `Contains` mapuje na [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="73c57-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="73c57-129">V SQLite s výchozí kolace je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="73c57-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="73c57-130">Přejděte na stránku filmy a připojte řetězec dotazu jako `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="73c57-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="73c57-131">Zobrazují se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="73c57-131">The filtered movies are displayed.</span></span>

![Index zobrazení](search/_static/ghost.png)

<span data-ttu-id="73c57-133">Pokud na indexovou stránku se přidá následující šablonu trasy, vyhledávací řetězec lze předat jako segment adresy URL (například `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="73c57-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="73c57-134">Předcházející omezení trasy umožňuje hledání názvu jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="73c57-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="73c57-135">`?` v `"{searchString?}"` znamená, že toto je parametr trasy nepovinný.</span><span class="sxs-lookup"><span data-stu-id="73c57-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Index zobrazení na mapách slovo ghost, přidá do adresy Url a vrácené film seznam dvou filmy, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="73c57-137">ASP.NET Core runtime používá [vazby modelu](xref:mvc/models/model-binding) nastavit hodnotu `SearchString` vlastnost z řetězce dotazu (`?searchString=Ghost`) nebo směrovat data (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="73c57-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="73c57-138">Vazby modelu se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="73c57-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="73c57-139">Ale nemůžete očekávat, že uživatelům změnit adresu URL pro hledání videa.</span><span class="sxs-lookup"><span data-stu-id="73c57-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="73c57-140">V tomto kroku se přidá uživatelského rozhraní pro filtrování videa.</span><span class="sxs-lookup"><span data-stu-id="73c57-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="73c57-141">Pokud jste přidali dané omezení trasy `"{searchString?}"`, odeberte ji.</span><span class="sxs-lookup"><span data-stu-id="73c57-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="73c57-142">Otevřít *Pages/Movies/Index.cshtml* a přidejte `<form>` značky v následujícím kódu zvýrazněno:</span><span class="sxs-lookup"><span data-stu-id="73c57-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="73c57-143">Kód HTML `<form>` značky používá následující [pomocných rutin značek](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="73c57-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="73c57-144">[Pomocná rutina značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="73c57-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="73c57-145">Když se odešle formulář, řetězec filtru posílá *stránek/filmy/Index* stránky pomocí řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="73c57-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="73c57-146">Pomocná rutina značky vstupu</span><span class="sxs-lookup"><span data-stu-id="73c57-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="73c57-147">Uložte změny a filtr otestovat.</span><span class="sxs-lookup"><span data-stu-id="73c57-147">Save the changes and test the filter.</span></span>

![Index zobrazení na mapách slovo ghost, zadaný do textového pole Název filtru](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="73c57-149">Hledat podle žánru</span><span class="sxs-lookup"><span data-stu-id="73c57-149">Search by genre</span></span>

<span data-ttu-id="73c57-150">Aktualizace `OnGetAsync` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="73c57-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="73c57-151">Následující kód je dotaz LINQ, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="73c57-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="73c57-152">`SelectList` Žánrů se vytvořila projekci odlišné žánrů.</span><span class="sxs-lookup"><span data-stu-id="73c57-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="73c57-153">Přidat hledání podle žánru pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="73c57-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="73c57-154">Aktualizace *Index.cshtml* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="73c57-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="73c57-155">Otestujte aplikaci tak, že žánr, název filmu a obě.</span><span class="sxs-lookup"><span data-stu-id="73c57-155">Test the app by searching by genre, by movie title, and by both.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="73c57-156">[Předchozí: Aktualizace stránek](xref:tutorials/razor-pages/da1)
> [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="73c57-156">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>
