---
title: Přidat hledání do ASP.NET Core Razor Pages
author: rick-anderson
description: Ukazuje, jak přidat hledání do ASP.NET Core Razor Pages
ms.author: riande
ms.date: 7/23/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: fde99d8fab00265294bccb2ecdb380d02a8f3673
ms.sourcegitcommit: a7813a776809a5029c94aa503ee71994f156231f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71267755"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="cf740-103">Přidat hledání do ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cf740-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="cf740-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cf740-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="cf740-105">V následujících částech se přidají hledání filmů podle *žánru* nebo *názvu* .</span><span class="sxs-lookup"><span data-stu-id="cf740-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="cf740-106">Přidejte následující zvýrazněné vlastnosti na *stránky/filmy/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="cf740-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="cf740-107">`SearchString`: obsahuje text, který uživatel zadá do textového pole Hledat.</span><span class="sxs-lookup"><span data-stu-id="cf740-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="cf740-108">`SearchString`je upraven pomocí [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atributu.</span><span class="sxs-lookup"><span data-stu-id="cf740-108">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="cf740-109">`[BindProperty]`vytvoří vazby na hodnoty formuláře a řetězce dotazů se stejným názvem jako má vlastnost.</span><span class="sxs-lookup"><span data-stu-id="cf740-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="cf740-110">`(SupportsGet = true)`se vyžaduje pro vazbu na požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="cf740-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="cf740-111">`Genres`: obsahuje seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="cf740-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="cf740-112">`Genres`umožňuje uživateli vybrat ze seznamu Žánr.</span><span class="sxs-lookup"><span data-stu-id="cf740-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="cf740-113">`SelectList`nutné`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="cf740-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="cf740-114">`MovieGenre`: obsahuje konkrétní Žánr, kterou uživatel vybere (například "Western").</span><span class="sxs-lookup"><span data-stu-id="cf740-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="cf740-115">`Genres`a `MovieGenre` používají se později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="cf740-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="cf740-116">Aktualizujte `OnGetAsync` metodu stránky indexu pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="cf740-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="cf740-117">První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="cf740-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="cf740-118">Dotaz je definován *pouze* v tomto okamžiku **, nebyl proto** spuštěn proti databázi.</span><span class="sxs-lookup"><span data-stu-id="cf740-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="cf740-119">`SearchString` Pokud vlastnost nemá hodnotu null nebo je prázdná, dotaz na filmy se upraví tak, aby se vyfiltroval v hledaném řetězci:</span><span class="sxs-lookup"><span data-stu-id="cf740-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="cf740-120">Kód je [výraz lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `s => s.Title.Contains()`</span><span class="sxs-lookup"><span data-stu-id="cf740-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cf740-121">Výrazy lambda se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je `Contains` například metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu).</span><span class="sxs-lookup"><span data-stu-id="cf740-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="cf740-122">Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody (například `Where` `Contains` nebo `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="cf740-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="cf740-123">Místo toho je provádění dotazu odloženo.</span><span class="sxs-lookup"><span data-stu-id="cf740-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="cf740-124">To znamená, že vyhodnocení výrazu je zpožděno, dokud se jeho realizovaná hodnota neopakuje nebo `ToListAsync` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="cf740-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="cf740-125">Další informace najdete v tématu [spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="cf740-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="cf740-126">**Poznámka:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="cf740-126">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="cf740-127">Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci.</span><span class="sxs-lookup"><span data-stu-id="cf740-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="cf740-128">Na SQL Server se `Contains` mapuje na [SQL jako](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="cf740-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="cf740-129">V SQLite s výchozí kolací rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="cf740-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="cf740-130">Přejděte na stránku filmy a přidejte řetězec `?searchString=Ghost` dotazu, například na adresu URL ( `https://localhost:5001/Movies?searchString=Ghost`například).</span><span class="sxs-lookup"><span data-stu-id="cf740-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="cf740-131">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="cf740-131">The filtered movies are displayed.</span></span>

![Zobrazení indexu](search/_static/ghost.png)

<span data-ttu-id="cf740-133">Pokud je na stránku indexu přidána následující šablona trasy, hledaný řetězec může být předán jako segment adresy URL (například `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="cf740-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="cf740-134">Předchozí omezení trasy umožňuje prohledávat název jako data směrování (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="cf740-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="cf740-135">`?` V`"{searchString?}"` nástroji se označuje jako volitelný parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="cf740-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="cf740-137">Modul runtime ASP.NET Core používá [vazbu modelu](xref:mvc/models/model-binding) k nastavení hodnoty `SearchString` vlastnosti z řetězce dotazu (`?searchString=Ghost`) nebo dat Route (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="cf740-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="cf740-138">Vazba modelu nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="cf740-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="cf740-139">Nemůžete ale očekávat, že uživatelé upraví adresu URL pro hledání videa.</span><span class="sxs-lookup"><span data-stu-id="cf740-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="cf740-140">V tomto kroku se přidají uživatelské rozhraní pro filtrování filmů.</span><span class="sxs-lookup"><span data-stu-id="cf740-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="cf740-141">Pokud jste přidali omezení `"{searchString?}"`trasy, odeberte ho.</span><span class="sxs-lookup"><span data-stu-id="cf740-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="cf740-142">Otevřete soubor *Pages/Movies/index. cshtml* a přidejte `<form>` kód zvýrazněný v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="cf740-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="cf740-143">Značka jazyka `<form>` HTML používá následující [pomocníky značek](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="cf740-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="cf740-144">[Pomocná pomůcka značek formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="cf740-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="cf740-145">Při odeslání formuláře se řetězec filtru pošle na stránku *stránky, filmy/indexování* prostřednictvím řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="cf740-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="cf740-146">Pomocná rutina značky vstupu</span><span class="sxs-lookup"><span data-stu-id="cf740-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="cf740-147">Uložte změny a otestujte filtr.</span><span class="sxs-lookup"><span data-stu-id="cf740-147">Save the changes and test the filter.</span></span>

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="cf740-149">Hledat podle žánru</span><span class="sxs-lookup"><span data-stu-id="cf740-149">Search by genre</span></span>

<span data-ttu-id="cf740-150">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="cf740-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="cf740-151">Následující kód je dotaz LINQ, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="cf740-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="cf740-152">`SelectList` Žánry jsou vytvořeny vyvoláním různých žánrů.</span><span class="sxs-lookup"><span data-stu-id="cf740-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="cf740-153">Přidat hledání podle žánru na stránku Razor</span><span class="sxs-lookup"><span data-stu-id="cf740-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="cf740-154">Aktualizujte *index. cshtml* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="cf740-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="cf740-155">Otestujte aplikaci tak, že vyhledáte podle žánru, podle názvu filmu a pomocí obou.</span><span class="sxs-lookup"><span data-stu-id="cf740-155">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf740-156">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cf740-156">Additional resources</span></span>

* [<span data-ttu-id="cf740-157">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="cf740-157">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="cf740-158">[Předchozí Další aktualizace stránek](xref:tutorials/razor-pages/da1):
> [ Přidání nového pole](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="cf740-158">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="cf740-159">V následujících částech se přidají hledání filmů podle *žánru* nebo *názvu* .</span><span class="sxs-lookup"><span data-stu-id="cf740-159">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="cf740-160">Přidejte následující zvýrazněné vlastnosti na *stránky/filmy/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="cf740-160">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="cf740-161">`SearchString`: obsahuje text, který uživatel zadá do textového pole Hledat.</span><span class="sxs-lookup"><span data-stu-id="cf740-161">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="cf740-162">`SearchString`je upraven pomocí [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atributu.</span><span class="sxs-lookup"><span data-stu-id="cf740-162">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="cf740-163">`[BindProperty]`vytvoří vazby na hodnoty formuláře a řetězce dotazů se stejným názvem jako má vlastnost.</span><span class="sxs-lookup"><span data-stu-id="cf740-163">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="cf740-164">`(SupportsGet = true)`se vyžaduje pro vazbu na požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="cf740-164">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="cf740-165">`Genres`: obsahuje seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="cf740-165">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="cf740-166">`Genres`umožňuje uživateli vybrat ze seznamu Žánr.</span><span class="sxs-lookup"><span data-stu-id="cf740-166">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="cf740-167">`SelectList`nutné`using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="cf740-167">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="cf740-168">`MovieGenre`: obsahuje konkrétní Žánr, kterou uživatel vybere (například "Western").</span><span class="sxs-lookup"><span data-stu-id="cf740-168">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="cf740-169">`Genres`a `MovieGenre` používají se později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="cf740-169">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="cf740-170">Aktualizujte `OnGetAsync` metodu stránky indexu pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="cf740-170">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="cf740-171">První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="cf740-171">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="cf740-172">Dotaz je definován *pouze* v tomto okamžiku **, nebyl proto** spuštěn proti databázi.</span><span class="sxs-lookup"><span data-stu-id="cf740-172">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="cf740-173">`SearchString` Pokud vlastnost nemá hodnotu null nebo je prázdná, dotaz na filmy se upraví tak, aby se vyfiltroval v hledaném řetězci:</span><span class="sxs-lookup"><span data-stu-id="cf740-173">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="cf740-174">Kód je [výraz lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `s => s.Title.Contains()`</span><span class="sxs-lookup"><span data-stu-id="cf740-174">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cf740-175">Výrazy lambda se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je `Contains` například metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu).</span><span class="sxs-lookup"><span data-stu-id="cf740-175">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="cf740-176">Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody (například `Where` `Contains` nebo `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="cf740-176">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="cf740-177">Místo toho je provádění dotazu odloženo.</span><span class="sxs-lookup"><span data-stu-id="cf740-177">Rather, query execution is deferred.</span></span> <span data-ttu-id="cf740-178">To znamená, že vyhodnocení výrazu je zpožděno, dokud se jeho realizovaná hodnota neopakuje nebo `ToListAsync` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="cf740-178">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="cf740-179">Další informace najdete v tématu [spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .</span><span class="sxs-lookup"><span data-stu-id="cf740-179">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="cf740-180">**Poznámka:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v C# kódu.</span><span class="sxs-lookup"><span data-stu-id="cf740-180">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="cf740-181">Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci.</span><span class="sxs-lookup"><span data-stu-id="cf740-181">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="cf740-182">Na SQL Server se `Contains` mapuje na [SQL jako](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="cf740-182">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="cf740-183">V SQLite s výchozí kolací rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="cf740-183">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="cf740-184">Přejděte na stránku filmy a přidejte řetězec `?searchString=Ghost` dotazu, například na adresu URL ( `https://localhost:5001/Movies?searchString=Ghost`například).</span><span class="sxs-lookup"><span data-stu-id="cf740-184">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="cf740-185">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="cf740-185">The filtered movies are displayed.</span></span>

![Zobrazení indexu](search/_static/ghost.png)

<span data-ttu-id="cf740-187">Pokud je na stránku indexu přidána následující šablona trasy, hledaný řetězec může být předán jako segment adresy URL (například `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="cf740-187">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="cf740-188">Předchozí omezení trasy umožňuje prohledávat název jako data směrování (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="cf740-188">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="cf740-189">`?` V`"{searchString?}"` nástroji se označuje jako volitelný parametr trasy.</span><span class="sxs-lookup"><span data-stu-id="cf740-189">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="cf740-191">Modul runtime ASP.NET Core používá [vazbu modelu](xref:mvc/models/model-binding) k nastavení hodnoty `SearchString` vlastnosti z řetězce dotazu (`?searchString=Ghost`) nebo dat Route (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="cf740-191">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="cf740-192">Vazba modelu nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="cf740-192">Model binding is not case sensitive.</span></span>

<span data-ttu-id="cf740-193">Nemůžete ale očekávat, že uživatelé upraví adresu URL pro hledání videa.</span><span class="sxs-lookup"><span data-stu-id="cf740-193">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="cf740-194">V tomto kroku se přidají uživatelské rozhraní pro filtrování filmů.</span><span class="sxs-lookup"><span data-stu-id="cf740-194">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="cf740-195">Pokud jste přidali omezení `"{searchString?}"`trasy, odeberte ho.</span><span class="sxs-lookup"><span data-stu-id="cf740-195">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="cf740-196">Otevřete soubor *Pages/Movies/index. cshtml* a přidejte `<form>` kód zvýrazněný v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="cf740-196">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="cf740-197">Značka jazyka `<form>` HTML používá následující [pomocníky značek](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="cf740-197">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="cf740-198">[Pomocná pomůcka značek formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="cf740-198">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="cf740-199">Při odeslání formuláře se řetězec filtru pošle na stránku *stránky, filmy/indexování* prostřednictvím řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="cf740-199">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="cf740-200">Pomocná rutina značky vstupu</span><span class="sxs-lookup"><span data-stu-id="cf740-200">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="cf740-201">Uložte změny a otestujte filtr.</span><span class="sxs-lookup"><span data-stu-id="cf740-201">Save the changes and test the filter.</span></span>

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="cf740-203">Hledat podle žánru</span><span class="sxs-lookup"><span data-stu-id="cf740-203">Search by genre</span></span>

<span data-ttu-id="cf740-204">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="cf740-204">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="cf740-205">Následující kód je dotaz LINQ, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="cf740-205">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="cf740-206">`SelectList` Žánry jsou vytvořeny vyvoláním různých žánrů.</span><span class="sxs-lookup"><span data-stu-id="cf740-206">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="cf740-207">Přidat hledání podle žánru na stránku Razor</span><span class="sxs-lookup"><span data-stu-id="cf740-207">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="cf740-208">Aktualizujte *index. cshtml* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="cf740-208">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="cf740-209">Otestujte aplikaci tak, že vyhledáte podle žánru, podle názvu filmu a pomocí obou.</span><span class="sxs-lookup"><span data-stu-id="cf740-209">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="cf740-210">Předchozí kód používá pomocníka pro [výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper) a pomocníka značek možností.</span><span class="sxs-lookup"><span data-stu-id="cf740-210">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf740-211">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cf740-211">Additional resources</span></span>

* [<span data-ttu-id="cf740-212">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="cf740-212">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="cf740-213">[Předchozí Další aktualizace stránek](xref:tutorials/razor-pages/da1):
> [ Přidání nového pole](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="cf740-213">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
