---
title: Přidání vyhledávání do aplikace ASP.NET Core MVC
author: rick-anderson
description: Ukazuje, jak přidat vyhledávání do základní aplikace ASP.NET Core MVC
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662867"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="1dc46-103">Přidání vyhledávání do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1dc46-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="1dc46-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1dc46-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1dc46-105">V této části přidáte funkci `Index` vyhledávání k metodě akce, která umožňuje vyhledávat filmy podle *žánru* nebo *názvu*.</span><span class="sxs-lookup"><span data-stu-id="1dc46-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="1dc46-106">Aktualizujte `Index` metodu nalezenou uvnitř *Controllers/MoviesController.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="1dc46-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="1dc46-107">První řádek metody `Index` akce vytvoří dotaz [LINQ](/dotnet/standard/using-linq) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="1dc46-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="1dc46-108">Dotaz je definován *pouze* v tomto okamžiku, **nebyl** spuštěn proti databázi.</span><span class="sxs-lookup"><span data-stu-id="1dc46-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="1dc46-109">Pokud `searchString` parametr obsahuje řetězec, dotaz na filmy se upraví tak, aby filtroval hodnotu hledaného řetězce:</span><span class="sxs-lookup"><span data-stu-id="1dc46-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="1dc46-110">Výše `s => s.Title.Contains()` uvedený kód je [Lambda výraz](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="1dc46-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="1dc46-111">Lambdas se používají v dotazech [LINQ](/dotnet/standard/using-linq) založených na metodách jako argumenty `Contains` pro standardní metody operátoru dotazu, jako je například metoda [Where](/dotnet/api/system.linq.enumerable.where) nebo (použitá ve výše uvedeném kódu).</span><span class="sxs-lookup"><span data-stu-id="1dc46-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="1dc46-112">Linq dotazy nejsou spouštěny, když jsou definovány nebo když jsou `Where`změněny voláním metody, jako je například , `Contains`nebo `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="1dc46-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="1dc46-113">Spíše je odloženo spuštění dotazu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="1dc46-114">To znamená, že vyhodnocení výrazu je zpožděno, dokud jeho realizovaná hodnota není skutečně iterována nad nebo `ToListAsync` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="1dc46-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="1dc46-115">Další informace o spuštění odloženého dotazu naleznete v [tématu Spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="1dc46-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="1dc46-116">Poznámka: Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu c#, který je uveden výše.</span><span class="sxs-lookup"><span data-stu-id="1dc46-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="1dc46-117">Rozlišování malých a velkých písmen na dotaz závisí na databázi a řazení.</span><span class="sxs-lookup"><span data-stu-id="1dc46-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="1dc46-118">Na SQL [Server, Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="1dc46-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="1dc46-119">V SQLite, s výchozí řazení, je rozlišována malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="1dc46-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="1dc46-120">Přejděte na adresu `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="1dc46-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="1dc46-121">Přidejte řetězec dotazu, například `?searchString=Ghost` k adrese URL.</span><span class="sxs-lookup"><span data-stu-id="1dc46-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="1dc46-122">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="1dc46-122">The filtered movies are displayed.</span></span>

![Zobrazení indexu](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="1dc46-124">Pokud změníte podpis `Index` metody tak, aby `id`byl `id` parametr pojmenován `{id}` , bude parametr odpovídat volitelnému zástupný symbol pro výchozí trasy nastavené v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1dc46-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="1dc46-125">Změňte parametr `id` na a `searchString` všechny `id`výskyty změn na .</span><span class="sxs-lookup"><span data-stu-id="1dc46-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="1dc46-126">Předchozí `Index` metoda:</span><span class="sxs-lookup"><span data-stu-id="1dc46-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="1dc46-127">`Index` Aktualizovaná `id` metoda s parametrem:</span><span class="sxs-lookup"><span data-stu-id="1dc46-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="1dc46-128">Nyní můžete předat název hledání jako data trasy (segment adresy URL) namísto jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Zobrazení indexu se slovem ghost přidané do url a vrácený seznam filmů dvou filmů, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="1dc46-130">Nelze však očekávat, že uživatelé změní adresu URL pokaždé, když chtějí vyhledat film.</span><span class="sxs-lookup"><span data-stu-id="1dc46-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="1dc46-131">Takže teď přidáte prvky uživatelského rozhraní, které jim pomohou filtrovat filmy.</span><span class="sxs-lookup"><span data-stu-id="1dc46-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="1dc46-132">Pokud jste změnili `Index` podpis metody a otestovali, `ID` jak předat parametr vázaný na trasu, změňte jej zpět tak, aby byl parametr pojmenován `searchString`:</span><span class="sxs-lookup"><span data-stu-id="1dc46-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="1dc46-133">Otevřete soubor *Zobrazení/Filmy/Index.cshtml* a `<form>` přidejte níže zvýrazněné značky:</span><span class="sxs-lookup"><span data-stu-id="1dc46-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="1dc46-134">Značka `<form>` HTML používá [pomocníka pro označení formuláře](xref:mvc/views/working-with-forms), takže při odeslání formuláře `Index` je řetězec filtru zaúčtován do akce řadiče filmů.</span><span class="sxs-lookup"><span data-stu-id="1dc46-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="1dc46-135">Uložte změny a potom filtr otestujte.</span><span class="sxs-lookup"><span data-stu-id="1dc46-135">Save your changes and then test the filter.</span></span>

![Zobrazení rejstříku se slovem ghost zadaným do textového pole filtru Nadpis](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="1dc46-137">Neexistuje žádné `[HttpPost]` přetížení metody, `Index` jak byste mohli očekávat.</span><span class="sxs-lookup"><span data-stu-id="1dc46-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="1dc46-138">Nepotřebujete ji, protože metoda nemění stav aplikace, jen filtrování dat.</span><span class="sxs-lookup"><span data-stu-id="1dc46-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="1dc46-139">Můžete přidat následující `[HttpPost] Index` metodu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="1dc46-140">Parametr `notUsed` se používá k vytvoření `Index` přetížení pro metodu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="1dc46-141">O tom si promluvíme později v tutoriálu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="1dc46-142">Pokud přidáte tuto metodu, vyvolávač akce by odpovídal metodě `[HttpPost] Index` a `[HttpPost] Index` metoda by se spouštěla, jak je znázorněno na obrázku níže.</span><span class="sxs-lookup"><span data-stu-id="1dc46-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Okno prohlížeče s odpovědí aplikace Z HttpPost Index: filtr na ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="1dc46-144">Však i v `[HttpPost]` případě, `Index` že přidáte tuto verzi metody, je omezení v tom, jak to všechno bylo implementováno.</span><span class="sxs-lookup"><span data-stu-id="1dc46-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="1dc46-145">Představte si, že chcete přidat do záložek určité vyhledávání nebo chcete poslat odkaz přátelům, na které mohou kliknout, aby viděli stejný filtrovaný seznam filmů.</span><span class="sxs-lookup"><span data-stu-id="1dc46-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="1dc46-146">Všimněte si, že adresa URL pro požadavek HTTP POST je stejná jako adresa URL pro požadavek GET (localhost:{PORT}/Movies/Index) – v adrese URL nejsou žádné informace o hledání.</span><span class="sxs-lookup"><span data-stu-id="1dc46-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="1dc46-147">Informace o vyhledávacím řetězci jsou odesílány na server jako [hodnota pole formuláře](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="1dc46-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="1dc46-148">Můžete ověřit, že s prohlížečem Developer tools nebo vynikající [Fiddler nástroj](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="1dc46-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="1dc46-149">Na obrázku níže jsou zobrazeny nástroje pro vývojáře prohlížeče Chrome:</span><span class="sxs-lookup"><span data-stu-id="1dc46-149">The image below shows the Chrome browser Developer tools:</span></span>

![Karta Síť nástrojů pro vývojáře v microsoft edge zobrazující tělo požadavku s hodnotou searchString ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="1dc46-151">Můžete vidět vyhledávací parametr a [token XSRF](xref:security/anti-request-forgery) v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="1dc46-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="1dc46-152">Všimněte si, jak je uvedeno v předchozím kurzu, [pomocné s tagem formuláře](xref:mvc/views/working-with-forms) generuje token anti-padělání [XSRF.](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="1dc46-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="1dc46-153">Neupravujeme data, takže nepotřebujeme ověřit token v metodě kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1dc46-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="1dc46-154">Vzhledem k tomu, že parametr hledání je v textu požadavku a není adresa URL, nelze zachytit, že informace o hledání záložku nebo sdílet s ostatními.</span><span class="sxs-lookup"><span data-stu-id="1dc46-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="1dc46-155">Opravte to zadáním požadavku, který by měl být `HTTP GET` nalezen v souboru *Zobrazení/Filmy/Index.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="1dc46-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="1dc46-156">Nyní při odeslání hledání obsahuje adresa URL řetězec vyhledávacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="1dc46-157">Hledání přejde také `HttpGet Index` na metodu akce, `HttpPost Index` i když máte metodu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Okno prohlížeče zobrazující searchString = duch v Url a filmy se vrátil, Ghostbusters a Ghostbusters 2, obsahují slovo ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="1dc46-159">Následující značky ukazují změnu `form` značky:</span><span class="sxs-lookup"><span data-stu-id="1dc46-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="1dc46-160">Přidat hledání podle žánru</span><span class="sxs-lookup"><span data-stu-id="1dc46-160">Add Search by genre</span></span>

<span data-ttu-id="1dc46-161">Přidejte `MovieGenreViewModel` do složky *Modely* následující třídu:</span><span class="sxs-lookup"><span data-stu-id="1dc46-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="1dc46-162">Model zobrazení filmového žánru bude obsahovat:</span><span class="sxs-lookup"><span data-stu-id="1dc46-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="1dc46-163">Seznam filmů.</span><span class="sxs-lookup"><span data-stu-id="1dc46-163">A list of movies.</span></span>
* <span data-ttu-id="1dc46-164">A `SelectList` obsahující seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="1dc46-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="1dc46-165">To umožňuje uživateli vybrat žánr ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="1dc46-166">`MovieGenre`, který obsahuje vybraný žánr.</span><span class="sxs-lookup"><span data-stu-id="1dc46-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="1dc46-167">`SearchString`, který obsahuje text, který uživatelé zadají do vyhledávacího textového pole.</span><span class="sxs-lookup"><span data-stu-id="1dc46-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="1dc46-168">Nahraďte `Index` `MoviesController.cs` metodu v s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1dc46-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="1dc46-169">Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="1dc46-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="1dc46-170">Žánry `SelectList` jsou vytvářeny promítáním odlišných žánrů (nechceme, aby náš výběrový seznam měl duplicitní žánry).</span><span class="sxs-lookup"><span data-stu-id="1dc46-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="1dc46-171">Když uživatel vyhledá položku, hodnota hledání zůstane zachována ve vyhledávacím poli.</span><span class="sxs-lookup"><span data-stu-id="1dc46-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="1dc46-172">Přidání hledání podle žánru do zobrazení rejstříku</span><span class="sxs-lookup"><span data-stu-id="1dc46-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="1dc46-173">Aktualizace `Index.cshtml` nalezena v *Zobrazení / Filmy /* takto:</span><span class="sxs-lookup"><span data-stu-id="1dc46-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="1dc46-174">Zkontrolujte výraz lambda použitý v následujícím pomocném prvku HTML:</span><span class="sxs-lookup"><span data-stu-id="1dc46-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="1dc46-175">V předchozím kódu pomocník `DisplayNameFor` HTML kontroluje `Title` vlastnost, na kterou odkazuje výraz lambda, aby zjistil zobrazovaný název.</span><span class="sxs-lookup"><span data-stu-id="1dc46-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1dc46-176">Vzhledem k tomu, že výraz lambda je kontrolována spíše `model` `model.Movies`než `model.Movies[0]` vyhodnocena, neobdržíte narušení přístupu, když , , nebo jsou `null` nebo prázdné.</span><span class="sxs-lookup"><span data-stu-id="1dc46-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="1dc46-177">Při vyhodnocení výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnoceny hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="1dc46-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="1dc46-178">Otestujte aplikaci podle žánru, názvu filmu a podle obou:</span><span class="sxs-lookup"><span data-stu-id="1dc46-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Okno prohlížeče zobrazující výsledkyhttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="1dc46-180">[Předchozí](controller-methods-views.md)
> [další](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="1dc46-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
