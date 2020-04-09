---
title: Přidání vyhledávání na stránky ASP.NET Core Razor
author: rick-anderson
description: Ukazuje, jak přidat vyhledávání na ASP.NET Core Razor Stránky
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8228207b0f37a6923b29891ac3115dd0be115501
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667704"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="02d59-103">Přidání vyhledávání na stránky ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="02d59-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="02d59-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02d59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="02d59-105">V následujících částech je přidáno vyhledávání filmů podle *žánru* nebo *názvu.*</span><span class="sxs-lookup"><span data-stu-id="02d59-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="02d59-106">Přidejte následující zvýrazněné vlastnosti do *stránek/filmů/index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="02d59-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="02d59-107">`SearchString`: obsahuje text, který uživatelé zadají do vyhledávacího textového pole.</span><span class="sxs-lookup"><span data-stu-id="02d59-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="02d59-108">`SearchString`má [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="02d59-108">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="02d59-109">`[BindProperty]`váže hodnoty formuláře a řetězce dotazů se stejným názvem jako vlastnost.</span><span class="sxs-lookup"><span data-stu-id="02d59-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="02d59-110">`(SupportsGet = true)`je vyžadována pro vazbu na požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="02d59-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="02d59-111">`Genres`: obsahuje seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="02d59-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="02d59-112">`Genres`umožňuje uživateli vybrat žánr ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="02d59-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="02d59-113">`SelectList`Vyžaduje`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="02d59-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="02d59-114">`MovieGenre`: obsahuje konkrétní žánr, který uživatel vybere (například "Západní").</span><span class="sxs-lookup"><span data-stu-id="02d59-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="02d59-115">`Genres`a `MovieGenre` jsou použity později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="02d59-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="02d59-116">Aktualizujte `OnGetAsync` metodu stránky Index následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02d59-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="02d59-117">První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="02d59-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="02d59-118">Dotaz je definován *pouze* v tomto okamžiku, **nebyl** spuštěn proti databázi.</span><span class="sxs-lookup"><span data-stu-id="02d59-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="02d59-119">Pokud `SearchString` vlastnost není null nebo prázdné, dotaz filmy je upraven tak, aby filtrovat na hledaný řetězec:</span><span class="sxs-lookup"><span data-stu-id="02d59-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="02d59-120">Kód `s => s.Title.Contains()` je [Lambda výraz](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="02d59-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="02d59-121">Lambdas se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty `Contains` pro standardní metody operátoru dotazu, jako je například metoda [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu).</span><span class="sxs-lookup"><span data-stu-id="02d59-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="02d59-122">Linq dotazy nejsou provedeny, když jsou definovány nebo když jsou změněny `Where`voláním metody (například , `Contains` nebo `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="02d59-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="02d59-123">Spíše je odloženo spuštění dotazu.</span><span class="sxs-lookup"><span data-stu-id="02d59-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="02d59-124">To znamená, že vyhodnocení výrazu je zpožděno, dokud není `ToListAsync` jeho realizovaná hodnota iterována nad nebo je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="02d59-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="02d59-125">Další informace naleznete v [tématu Spuštění dotazu.](/dotnet/framework/data/adonet/ef/language-reference/query-execution)</span><span class="sxs-lookup"><span data-stu-id="02d59-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="02d59-126">Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="02d59-126">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="02d59-127">Rozlišování malých a velkých písmen na dotaz závisí na databázi a řazení.</span><span class="sxs-lookup"><span data-stu-id="02d59-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="02d59-128">Na SQL `Contains` Server, mapuje [sql like](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="02d59-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="02d59-129">V SQLite, s výchozí řazení, je rozlišována malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="02d59-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="02d59-130">Přejděte na stránku Filmy a přidejte řetězec dotazu, například `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="02d59-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="02d59-131">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="02d59-131">The filtered movies are displayed.</span></span>

![Zobrazení indexu](search/_static/ghost.png)

<span data-ttu-id="02d59-133">Pokud je na stránku Index přidána následující šablona trasy, může být `https://localhost:5001/Movies/Ghost`hledaný řetězec předán jako segment adresy URL (například ).</span><span class="sxs-lookup"><span data-stu-id="02d59-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="02d59-134">Předchozí omezení trasy umožňuje prohledávat název jako data trasy (segment adresy URL) namísto jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="02d59-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="02d59-135">In `?` `"{searchString?}"` znamená, že se jedná o volitelný parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="02d59-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Zobrazení indexu se slovem ghost přidané do url a vrácený seznam filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="02d59-137">ASP.NET Core runtime používá [vazbu modelu](xref:mvc/models/model-binding) `SearchString` k nastavení hodnoty`?searchString=Ghost`vlastnosti z`https://localhost:5001/Movies/Ghost`řetězce dotazu ( ) nebo dat trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="02d59-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="02d59-138">Vazba modelu nerozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="02d59-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="02d59-139">Nelze však očekávat, že uživatelé změní adresu URL a vyhledávají film.</span><span class="sxs-lookup"><span data-stu-id="02d59-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="02d59-140">V tomto kroku je k filtrování filmů přidáno ui.</span><span class="sxs-lookup"><span data-stu-id="02d59-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="02d59-141">Pokud jste přidali `"{searchString?}"`omezení trasy , odeberte ho.</span><span class="sxs-lookup"><span data-stu-id="02d59-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="02d59-142">Otevřete soubor *Pages/Movies/Index.cshtml* a `<form>` přidejte značky zvýrazněné v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="02d59-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="02d59-143">Značka `<form>` HTML používá následující [pomocné síly značek](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="02d59-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="02d59-144">[Pomocné s označením formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="02d59-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="02d59-145">Po odeslání formuláře je řetězec filtru odeslán na stránku *Stránky/Filmy/Index* prostřednictvím řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="02d59-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="02d59-146">Pomocná rutina značky vstupu</span><span class="sxs-lookup"><span data-stu-id="02d59-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="02d59-147">Uložte změny a otestujte filtr.</span><span class="sxs-lookup"><span data-stu-id="02d59-147">Save the changes and test the filter.</span></span>

![Zobrazení rejstříku se slovem ghost zadaným do textového pole filtru Nadpis](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="02d59-149">Vyhledávání podle žánru</span><span class="sxs-lookup"><span data-stu-id="02d59-149">Search by genre</span></span>

<span data-ttu-id="02d59-150">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02d59-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="02d59-151">Následující kód je linq dotaz, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="02d59-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="02d59-152">Žánry `SelectList` jsou vytvářeny promítáním odlišných žánrů.</span><span class="sxs-lookup"><span data-stu-id="02d59-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="02d59-153">Přidání vyhledávání podle žánru na stránku Břitva</span><span class="sxs-lookup"><span data-stu-id="02d59-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="02d59-154">Aktualizujte *index.cshtml* takto:</span><span class="sxs-lookup"><span data-stu-id="02d59-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="02d59-155">Otestujte aplikaci podle žánru, názvu filmu a podle obou.</span><span class="sxs-lookup"><span data-stu-id="02d59-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02d59-156">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="02d59-156">Additional resources</span></span>

* [<span data-ttu-id="02d59-157">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="02d59-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="02d59-158">[Předchozí: Aktualizace stránek](xref:tutorials/razor-pages/da1)
> [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="02d59-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="02d59-159">V následujících částech je přidáno vyhledávání filmů podle *žánru* nebo *názvu.*</span><span class="sxs-lookup"><span data-stu-id="02d59-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="02d59-160">Přidejte následující zvýrazněné vlastnosti do *stránek/filmů/index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="02d59-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="02d59-161">`SearchString`: obsahuje text, který uživatelé zadají do vyhledávacího textového pole.</span><span class="sxs-lookup"><span data-stu-id="02d59-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="02d59-162">`SearchString`má [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="02d59-162">`SearchString` has the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="02d59-163">`[BindProperty]`váže hodnoty formuláře a řetězce dotazů se stejným názvem jako vlastnost.</span><span class="sxs-lookup"><span data-stu-id="02d59-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="02d59-164">`(SupportsGet = true)`je vyžadována pro vazbu na požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="02d59-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="02d59-165">`Genres`: obsahuje seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="02d59-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="02d59-166">`Genres`umožňuje uživateli vybrat žánr ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="02d59-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="02d59-167">`SelectList`Vyžaduje`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="02d59-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="02d59-168">`MovieGenre`: obsahuje konkrétní žánr, který uživatel vybere (například "Západní").</span><span class="sxs-lookup"><span data-stu-id="02d59-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="02d59-169">`Genres`a `MovieGenre` jsou použity později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="02d59-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="02d59-170">Aktualizujte `OnGetAsync` metodu stránky Index následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02d59-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="02d59-171">První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="02d59-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="02d59-172">Dotaz je definován *pouze* v tomto okamžiku, **nebyl** spuštěn proti databázi.</span><span class="sxs-lookup"><span data-stu-id="02d59-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="02d59-173">Pokud `SearchString` vlastnost není null nebo prázdné, dotaz filmy je upraven tak, aby filtrovat na hledaný řetězec:</span><span class="sxs-lookup"><span data-stu-id="02d59-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="02d59-174">Kód `s => s.Title.Contains()` je [Lambda výraz](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="02d59-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="02d59-175">Lambdas se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty `Contains` pro standardní metody operátoru dotazu, jako je například metoda [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu).</span><span class="sxs-lookup"><span data-stu-id="02d59-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="02d59-176">Linq dotazy nejsou provedeny, když jsou definovány nebo když jsou změněny `Where`voláním metody (například , `Contains` nebo `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="02d59-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="02d59-177">Spíše je odloženo spuštění dotazu.</span><span class="sxs-lookup"><span data-stu-id="02d59-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="02d59-178">To znamená, že vyhodnocení výrazu je zpožděno, dokud není `ToListAsync` jeho realizovaná hodnota iterována nad nebo je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="02d59-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="02d59-179">Další informace naleznete v [tématu Spuštění dotazu.](/dotnet/framework/data/adonet/ef/language-reference/query-execution)</span><span class="sxs-lookup"><span data-stu-id="02d59-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="02d59-180">**Poznámka:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="02d59-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="02d59-181">Rozlišování malých a velkých písmen na dotaz závisí na databázi a řazení.</span><span class="sxs-lookup"><span data-stu-id="02d59-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="02d59-182">Na SQL `Contains` Server, mapuje [sql like](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="02d59-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="02d59-183">V SQLite, s výchozí řazení, je rozlišována malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="02d59-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="02d59-184">Přejděte na stránku Filmy a přidejte řetězec dotazu, například `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="02d59-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="02d59-185">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="02d59-185">The filtered movies are displayed.</span></span>

![Zobrazení indexu](search/_static/ghost.png)

<span data-ttu-id="02d59-187">Pokud je na stránku Index přidána následující šablona trasy, může být `https://localhost:5001/Movies/Ghost`hledaný řetězec předán jako segment adresy URL (například ).</span><span class="sxs-lookup"><span data-stu-id="02d59-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="02d59-188">Předchozí omezení trasy umožňuje prohledávat název jako data trasy (segment adresy URL) namísto jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="02d59-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="02d59-189">In `?` `"{searchString?}"` znamená, že se jedná o volitelný parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="02d59-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Zobrazení indexu se slovem ghost přidané do url a vrácený seznam filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="02d59-191">ASP.NET Core runtime používá [vazbu modelu](xref:mvc/models/model-binding) `SearchString` k nastavení hodnoty`?searchString=Ghost`vlastnosti z`https://localhost:5001/Movies/Ghost`řetězce dotazu ( ) nebo dat trasy ( ).</span><span class="sxs-lookup"><span data-stu-id="02d59-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="02d59-192">Vazba modelu nerozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="02d59-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="02d59-193">Nelze však očekávat, že uživatelé změní adresu URL a vyhledávají film.</span><span class="sxs-lookup"><span data-stu-id="02d59-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="02d59-194">V tomto kroku je k filtrování filmů přidáno ui.</span><span class="sxs-lookup"><span data-stu-id="02d59-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="02d59-195">Pokud jste přidali `"{searchString?}"`omezení trasy , odeberte ho.</span><span class="sxs-lookup"><span data-stu-id="02d59-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="02d59-196">Otevřete soubor *Pages/Movies/Index.cshtml* a `<form>` přidejte značky zvýrazněné v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="02d59-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="02d59-197">Značka `<form>` HTML používá následující [pomocné síly značek](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="02d59-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="02d59-198">[Pomocné s označením formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="02d59-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="02d59-199">Po odeslání formuláře je řetězec filtru odeslán na stránku *Stránky/Filmy/Index* prostřednictvím řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="02d59-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="02d59-200">Pomocná rutina značky vstupu</span><span class="sxs-lookup"><span data-stu-id="02d59-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="02d59-201">Uložte změny a otestujte filtr.</span><span class="sxs-lookup"><span data-stu-id="02d59-201">Save the changes and test the filter.</span></span>

![Zobrazení rejstříku se slovem ghost zadaným do textového pole filtru Nadpis](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="02d59-203">Vyhledávání podle žánru</span><span class="sxs-lookup"><span data-stu-id="02d59-203">Search by genre</span></span>

<span data-ttu-id="02d59-204">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02d59-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="02d59-205">Následující kód je linq dotaz, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="02d59-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="02d59-206">Žánry `SelectList` jsou vytvářeny promítáním odlišných žánrů.</span><span class="sxs-lookup"><span data-stu-id="02d59-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="02d59-207">Přidání vyhledávání podle žánru na stránku Břitva</span><span class="sxs-lookup"><span data-stu-id="02d59-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="02d59-208">Aktualizujte *index.cshtml* takto:</span><span class="sxs-lookup"><span data-stu-id="02d59-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="02d59-209">Otestujte aplikaci podle žánru, názvu filmu a podle obou.</span><span class="sxs-lookup"><span data-stu-id="02d59-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="02d59-210">Předchozí kód používá [možnost Vybrat pomocnou položku značek](xref:mvc/views/working-with-forms#the-select-tag-helper) a pomocnou položku s opčními značkami.</span><span class="sxs-lookup"><span data-stu-id="02d59-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02d59-211">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="02d59-211">Additional resources</span></span>

* [<span data-ttu-id="02d59-212">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="02d59-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="02d59-213">[Předchozí: Aktualizace stránek](xref:tutorials/razor-pages/da1)
> [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="02d59-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
