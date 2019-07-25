---
title: Přidat hledání do ASP.NET Core Razor Pages
author: rick-anderson
description: Ukazuje, jak přidat hledání do ASP.NET Core Razor Pages
ms.author: riande
ms.date: 7/23/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: d1aa3f914ebcab4d095b6fca1dac3cf44855d516
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483290"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="443d3-103">Přidat hledání do ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="443d3-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="443d3-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="443d3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="443d3-105">V následujících částech se přidají hledání filmů  podle žánru nebo *názvu* .</span><span class="sxs-lookup"><span data-stu-id="443d3-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="443d3-106">Přidejte následující zvýrazněné vlastnosti na *stránky/filmy/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="443d3-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="443d3-107">`SearchString`: obsahuje text, který uživatel zadá do textového pole Hledat.</span><span class="sxs-lookup"><span data-stu-id="443d3-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="443d3-108">`SearchString`je upraven pomocí [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atributu.</span><span class="sxs-lookup"><span data-stu-id="443d3-108">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="443d3-109">`[BindProperty]`vytvoří vazby na hodnoty formuláře a řetězce dotazů se stejným názvem jako má vlastnost.</span><span class="sxs-lookup"><span data-stu-id="443d3-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="443d3-110">`(SupportsGet = true)`se vyžaduje pro vazbu na požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="443d3-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="443d3-111">`Genres`: obsahuje seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="443d3-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="443d3-112">`Genres`umožňuje uživateli vybrat ze seznamu Žánr.</span><span class="sxs-lookup"><span data-stu-id="443d3-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="443d3-113">`SelectList`nutné`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="443d3-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="443d3-114">`MovieGenre`: obsahuje konkrétní Žánr, kterou uživatel vybere (například "Western").</span><span class="sxs-lookup"><span data-stu-id="443d3-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="443d3-115">`Genres`a `MovieGenre` používají se později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="443d3-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="443d3-116">Aktualizujte `OnGetAsync` metodu stránky indexu pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="443d3-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="443d3-117">První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="443d3-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="443d3-118">Dotaz je definován *pouze* v tomto okamžiku, nebyl proto spuštěn  proti databázi.</span><span class="sxs-lookup"><span data-stu-id="443d3-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="443d3-119">`SearchString` Pokud vlastnost nemá hodnotu null nebo je prázdná, dotaz na filmy se upraví tak, aby se vyfiltroval v hledaném řetězci:</span><span class="sxs-lookup"><span data-stu-id="443d3-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="443d3-120">Kód je [výraz lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `s => s.Title.Contains()`</span><span class="sxs-lookup"><span data-stu-id="443d3-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="443d3-121">Výrazy lambda se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je `Contains` například metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu).</span><span class="sxs-lookup"><span data-stu-id="443d3-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="443d3-122">Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody (například `Where` `Contains` nebo `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="443d3-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="443d3-123">Místo toho je provádění dotazu odloženo.</span><span class="sxs-lookup"><span data-stu-id="443d3-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="443d3-124">To znamená, že vyhodnocení výrazu je zpožděno, dokud se jeho realizovaná hodnota neopakuje nebo `ToListAsync` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="443d3-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="443d3-125">Další informace najdete v tématu [spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="443d3-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="443d3-126">**Poznámka:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="443d3-126">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="443d3-127">Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci.</span><span class="sxs-lookup"><span data-stu-id="443d3-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="443d3-128">Na SQL Server se `Contains` mapuje na [SQL jako](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="443d3-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="443d3-129">V SQLite s výchozí kolací rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="443d3-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="443d3-130">Přejděte na stránku filmy a přidejte řetězec `?searchString=Ghost` dotazu, například na adresu URL ( `https://localhost:5001/Movies?searchString=Ghost`například).</span><span class="sxs-lookup"><span data-stu-id="443d3-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="443d3-131">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="443d3-131">The filtered movies are displayed.</span></span>

![Zobrazení indexu](search/_static/ghost.png)

<span data-ttu-id="443d3-133">Pokud je na stránku indexu přidána následující šablona trasy, hledaný řetězec může být předán jako segment adresy URL (například `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="443d3-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="443d3-134">Předchozí omezení trasy umožňuje prohledávat název jako data směrování (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="443d3-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="443d3-135">`?` V`"{searchString?}"` nástroji se označuje jako volitelný parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="443d3-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="443d3-137">Modul runtime ASP.NET Core používá [vazbu modelu](xref:mvc/models/model-binding) k nastavení hodnoty `SearchString` vlastnosti z řetězce dotazu (`?searchString=Ghost`) nebo dat Route (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="443d3-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="443d3-138">Vazba modelu nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="443d3-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="443d3-139">Nemůžete ale očekávat, že uživatelé upraví adresu URL pro hledání videa.</span><span class="sxs-lookup"><span data-stu-id="443d3-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="443d3-140">V tomto kroku se přidají uživatelské rozhraní pro filtrování filmů.</span><span class="sxs-lookup"><span data-stu-id="443d3-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="443d3-141">Pokud jste přidali omezení `"{searchString?}"`trasy, odeberte ho.</span><span class="sxs-lookup"><span data-stu-id="443d3-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="443d3-142">Otevřete soubor *Pages/Movies/index. cshtml* a přidejte `<form>` kód zvýrazněný v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="443d3-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="443d3-143">Značka jazyka `<form>` HTML používá následující [pomocníky značek](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="443d3-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="443d3-144">[Pomocná pomůcka značek formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="443d3-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="443d3-145">Při odeslání formuláře se řetězec filtru pošle na stránku *stránky, filmy/indexování* prostřednictvím řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="443d3-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="443d3-146">Pomocná rutina značky vstupu</span><span class="sxs-lookup"><span data-stu-id="443d3-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="443d3-147">Uložte změny a otestujte filtr.</span><span class="sxs-lookup"><span data-stu-id="443d3-147">Save the changes and test the filter.</span></span>

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="443d3-149">Hledat podle žánru</span><span class="sxs-lookup"><span data-stu-id="443d3-149">Search by genre</span></span>

<span data-ttu-id="443d3-150">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="443d3-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="443d3-151">Následující kód je dotaz LINQ, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="443d3-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="443d3-152">`SelectList` Žánry jsou vytvořeny vyvoláním různých žánrů.</span><span class="sxs-lookup"><span data-stu-id="443d3-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="443d3-153">Přidat hledání podle žánru na stránku Razor</span><span class="sxs-lookup"><span data-stu-id="443d3-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="443d3-154">Aktualizujte *index. cshtml* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="443d3-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="443d3-155">Otestujte aplikaci tak, že vyhledáte podle žánru, podle názvu filmu a pomocí obou.</span><span class="sxs-lookup"><span data-stu-id="443d3-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="443d3-156">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="443d3-156">Additional resources</span></span>

* [<span data-ttu-id="443d3-157">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="443d3-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="443d3-158">[Předchozí Další aktualizace stránek](xref:tutorials/razor-pages/da1):
> [ Přidání nového pole](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="443d3-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="443d3-159">V následujících částech se přidají hledání filmů  podle žánru nebo *názvu* .</span><span class="sxs-lookup"><span data-stu-id="443d3-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="443d3-160">Přidejte následující zvýrazněné vlastnosti na *stránky/filmy/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="443d3-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="443d3-161">`SearchString`: obsahuje text, který uživatel zadá do textového pole Hledat.</span><span class="sxs-lookup"><span data-stu-id="443d3-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="443d3-162">`SearchString`je upraven pomocí [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atributu.</span><span class="sxs-lookup"><span data-stu-id="443d3-162">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="443d3-163">`[BindProperty]`vytvoří vazby na hodnoty formuláře a řetězce dotazů se stejným názvem jako má vlastnost.</span><span class="sxs-lookup"><span data-stu-id="443d3-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="443d3-164">`(SupportsGet = true)`se vyžaduje pro vazbu na požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="443d3-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="443d3-165">`Genres`: obsahuje seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="443d3-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="443d3-166">`Genres`umožňuje uživateli vybrat ze seznamu Žánr.</span><span class="sxs-lookup"><span data-stu-id="443d3-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="443d3-167">`SelectList`nutné`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="443d3-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="443d3-168">`MovieGenre`: obsahuje konkrétní Žánr, kterou uživatel vybere (například "Western").</span><span class="sxs-lookup"><span data-stu-id="443d3-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="443d3-169">`Genres`a `MovieGenre` používají se později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="443d3-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="443d3-170">Aktualizujte `OnGetAsync` metodu stránky indexu pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="443d3-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="443d3-171">První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="443d3-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="443d3-172">Dotaz je definován *pouze* v tomto okamžiku, nebyl proto spuštěn  proti databázi.</span><span class="sxs-lookup"><span data-stu-id="443d3-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="443d3-173">`SearchString` Pokud vlastnost nemá hodnotu null nebo je prázdná, dotaz na filmy se upraví tak, aby se vyfiltroval v hledaném řetězci:</span><span class="sxs-lookup"><span data-stu-id="443d3-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="443d3-174">Kód je [výraz lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `s => s.Title.Contains()`</span><span class="sxs-lookup"><span data-stu-id="443d3-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="443d3-175">Výrazy lambda se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je `Contains` například metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu).</span><span class="sxs-lookup"><span data-stu-id="443d3-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="443d3-176">Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody (například `Where` `Contains` nebo `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="443d3-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="443d3-177">Místo toho je provádění dotazu odloženo.</span><span class="sxs-lookup"><span data-stu-id="443d3-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="443d3-178">To znamená, že vyhodnocení výrazu je zpožděno, dokud se jeho realizovaná hodnota neopakuje nebo `ToListAsync` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="443d3-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="443d3-179">Další informace najdete v tématu [spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="443d3-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="443d3-180">**Poznámka:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="443d3-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="443d3-181">Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci.</span><span class="sxs-lookup"><span data-stu-id="443d3-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="443d3-182">Na SQL Server se `Contains` mapuje na [SQL jako](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="443d3-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="443d3-183">V SQLite s výchozí kolací rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="443d3-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="443d3-184">Přejděte na stránku filmy a přidejte řetězec `?searchString=Ghost` dotazu, například na adresu URL ( `https://localhost:5001/Movies?searchString=Ghost`například).</span><span class="sxs-lookup"><span data-stu-id="443d3-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="443d3-185">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="443d3-185">The filtered movies are displayed.</span></span>

![Zobrazení indexu](search/_static/ghost.png)

<span data-ttu-id="443d3-187">Pokud je na stránku indexu přidána následující šablona trasy, hledaný řetězec může být předán jako segment adresy URL (například `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="443d3-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="443d3-188">Předchozí omezení trasy umožňuje prohledávat název jako data směrování (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="443d3-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="443d3-189">`?` V`"{searchString?}"` nástroji se označuje jako volitelný parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="443d3-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="443d3-191">Modul runtime ASP.NET Core používá [vazbu modelu](xref:mvc/models/model-binding) k nastavení hodnoty `SearchString` vlastnosti z řetězce dotazu (`?searchString=Ghost`) nebo dat Route (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="443d3-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="443d3-192">Vazba modelu nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="443d3-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="443d3-193">Nemůžete ale očekávat, že uživatelé upraví adresu URL pro hledání videa.</span><span class="sxs-lookup"><span data-stu-id="443d3-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="443d3-194">V tomto kroku se přidají uživatelské rozhraní pro filtrování filmů.</span><span class="sxs-lookup"><span data-stu-id="443d3-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="443d3-195">Pokud jste přidali omezení `"{searchString?}"`trasy, odeberte ho.</span><span class="sxs-lookup"><span data-stu-id="443d3-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="443d3-196">Otevřete soubor *Pages/Movies/index. cshtml* a přidejte `<form>` kód zvýrazněný v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="443d3-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="443d3-197">Značka jazyka `<form>` HTML používá následující [pomocníky značek](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="443d3-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="443d3-198">[Pomocná pomůcka značek formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="443d3-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="443d3-199">Při odeslání formuláře se řetězec filtru pošle na stránku *stránky, filmy/indexování* prostřednictvím řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="443d3-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="443d3-200">Pomocná rutina značky vstupu</span><span class="sxs-lookup"><span data-stu-id="443d3-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="443d3-201">Uložte změny a otestujte filtr.</span><span class="sxs-lookup"><span data-stu-id="443d3-201">Save the changes and test the filter.</span></span>

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="443d3-203">Hledat podle žánru</span><span class="sxs-lookup"><span data-stu-id="443d3-203">Search by genre</span></span>

<span data-ttu-id="443d3-204">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="443d3-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="443d3-205">Následující kód je dotaz LINQ, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="443d3-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="443d3-206">`SelectList` Žánry jsou vytvořeny vyvoláním různých žánrů.</span><span class="sxs-lookup"><span data-stu-id="443d3-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="443d3-207">Přidat hledání podle žánru na stránku Razor</span><span class="sxs-lookup"><span data-stu-id="443d3-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="443d3-208">Aktualizujte *index. cshtml* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="443d3-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="443d3-209">Otestujte aplikaci tak, že vyhledáte podle žánru, podle názvu filmu a pomocí obou.</span><span class="sxs-lookup"><span data-stu-id="443d3-209">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="443d3-210">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="443d3-210">Additional resources</span></span>

* [<span data-ttu-id="443d3-211">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="443d3-211">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="443d3-212">[Předchozí Další aktualizace stránek](xref:tutorials/razor-pages/da1):
> [ Přidání nového pole](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="443d3-212">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end