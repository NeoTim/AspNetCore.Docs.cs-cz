---
title: Přidání vyhledávání do aplikace ASP.NET Core MVC
author: rick-anderson
description: Ukazuje, jak přidat hledání do základní ASP.NET Core aplikace MVC.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 97ee5f66c142780d54d28013c109da61241d967b
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862948"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="1fb89-103">Přidání vyhledávání do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1fb89-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="1fb89-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1fb89-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1fb89-105">V této části přidáte funkci hledání do `Index` metody Action, která umožňuje hledat filmy podle *žánru* nebo *názvu*.</span><span class="sxs-lookup"><span data-stu-id="1fb89-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="1fb89-106">Aktualizujte metodu nalezenou v *Controllers/MoviesController. cs* pomocí následujícího kódu: `Index`</span><span class="sxs-lookup"><span data-stu-id="1fb89-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="1fb89-107">První řádek `Index` metody akce vytvoří dotaz [LINQ](/dotnet/standard/using-linq) pro výběr filmů:</span><span class="sxs-lookup"><span data-stu-id="1fb89-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="1fb89-108">Dotaz je definován *pouze* v tomto okamžiku, nebyl proto spuštěn proti databázi.</span><span class="sxs-lookup"><span data-stu-id="1fb89-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="1fb89-109">`searchString` Pokud parametr obsahuje řetězec, dotaz na filmy se upraví tak, aby se vyfiltroval na hodnotu hledaného řetězce:</span><span class="sxs-lookup"><span data-stu-id="1fb89-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="1fb89-110">Výše uvedený kód je [výraz lambda.](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) `s => s.Title.Contains()`</span><span class="sxs-lookup"><span data-stu-id="1fb89-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="1fb89-111">Výrazy lambda se používají v dotazech [LINQ](/dotnet/standard/using-linq) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je `Contains` například metoda [WHERE](/dotnet/api/system.linq.enumerable.where) nebo (použitá v kódu výše).</span><span class="sxs-lookup"><span data-stu-id="1fb89-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="1fb89-112">Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody `Where`, jako je, `Contains`nebo `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="1fb89-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="1fb89-113">Místo toho je provádění dotazu odloženo.</span><span class="sxs-lookup"><span data-stu-id="1fb89-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="1fb89-114">To znamená, že vyhodnocení výrazu je zpožděno, dokud se na jeho realizované hodnotě neprovádí iterace nebo `ToListAsync` je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="1fb89-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="1fb89-115">Další informace o odložení provádění dotazů naleznete v tématu [provádění dotazů](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="1fb89-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="1fb89-116">Poznámka: Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka c# zobrazeném výše.</span><span class="sxs-lookup"><span data-stu-id="1fb89-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="1fb89-117">Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci.</span><span class="sxs-lookup"><span data-stu-id="1fb89-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="1fb89-118">V SQL Server [obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy [jako SQL](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="1fb89-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="1fb89-119">V SQLite s výchozí kolací rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="1fb89-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="1fb89-120">Přejděte na adresu `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="1fb89-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="1fb89-121">Připojit řetězec dotazu, například `?searchString=Ghost` k adrese URL.</span><span class="sxs-lookup"><span data-stu-id="1fb89-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="1fb89-122">Zobrazí se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="1fb89-122">The filtered movies are displayed.</span></span>

![Zobrazení indexu](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="1fb89-124">Změníte `Index` -li podpis metody tak, aby měl parametr s názvem `id`, `id` parametr bude odpovídat volitelnému `{id}` zástupnému symbolu pro výchozí trasy nastavené v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="1fb89-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="1fb89-125">Změňte parametr na `id` a všechny výskyty `searchString` změny na `id`.</span><span class="sxs-lookup"><span data-stu-id="1fb89-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="1fb89-126">Předchozí `Index` metoda:</span><span class="sxs-lookup"><span data-stu-id="1fb89-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="1fb89-127">Aktualizovaná `Index` metoda s `id` parametrem:</span><span class="sxs-lookup"><span data-stu-id="1fb89-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="1fb89-128">Nyní můžete název hledání předat jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="1fb89-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="1fb89-130">Nemůžete ale očekávat, že uživatelé můžou upravovat adresu URL pokaždé, když chtějí hledat film.</span><span class="sxs-lookup"><span data-stu-id="1fb89-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="1fb89-131">Takže teď přidáte prvky uživatelského rozhraní, které jim pomůžou filtrovat filmy.</span><span class="sxs-lookup"><span data-stu-id="1fb89-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="1fb89-132">Pokud jste změnili signaturu `Index` metody, abyste otestovali, jak předat parametr vázaný `ID` na trase, změňte ho tak, aby přebírá parametr s `searchString`názvem:</span><span class="sxs-lookup"><span data-stu-id="1fb89-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="1fb89-133">Otevřete soubor *views/Movies/index. cshtml* a přidejte následující `<form>` kód, který je zvýrazněný níže:</span><span class="sxs-lookup"><span data-stu-id="1fb89-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="1fb89-134">Značka HTML `<form>` používá pomocníka [značek Form](xref:mvc/views/working-with-forms), takže když formulář odešlete, vystaví se řetězec filtru do `Index` akce kontroleru filmů.</span><span class="sxs-lookup"><span data-stu-id="1fb89-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="1fb89-135">Uložte změny a potom filtr otestujte.</span><span class="sxs-lookup"><span data-stu-id="1fb89-135">Save your changes and then test the filter.</span></span>

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="1fb89-137">`[HttpPost]` Neexistuje přetížení `Index` metody, jak by bylo možné očekávat.</span><span class="sxs-lookup"><span data-stu-id="1fb89-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="1fb89-138">Nepotřebujete ho, protože metoda nemění stav aplikace, stačí data filtrovat.</span><span class="sxs-lookup"><span data-stu-id="1fb89-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="1fb89-139">Můžete přidat následující `[HttpPost] Index` metodu.</span><span class="sxs-lookup"><span data-stu-id="1fb89-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="1fb89-140">Parametr se používá k vytvoření přetížení `Index` pro metodu. `notUsed`</span><span class="sxs-lookup"><span data-stu-id="1fb89-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="1fb89-141">O tom budeme mluvit později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="1fb89-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="1fb89-142">Pokud přidáte tuto metodu, akce původce by odpovídala `[HttpPost] Index` metodě a metoda se `[HttpPost] Index` spustí, jak je znázorněno na následujícím obrázku.</span><span class="sxs-lookup"><span data-stu-id="1fb89-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Okno prohlížeče s odpovědí na aplikaci z indexu HttpPost: filtrování na Ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="1fb89-144">Nicméně i v případě, že přidáte `[HttpPost]` tuto verzi `Index` metody, existuje omezení, jak je vše implementováno.</span><span class="sxs-lookup"><span data-stu-id="1fb89-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="1fb89-145">Představte si, že chcete vytvořit záložku konkrétního hledání nebo chcete poslat odkaz na přátele, na které můžou kliknout, aby se zobrazil stejný filtrovaný seznam filmů.</span><span class="sxs-lookup"><span data-stu-id="1fb89-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="1fb89-146">Všimněte si, že adresa URL požadavku HTTP POST je stejná jako adresa URL žádosti GET (localhost: {PORT}/video/index) – v adrese URL nejsou žádné vyhledávací informace.</span><span class="sxs-lookup"><span data-stu-id="1fb89-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="1fb89-147">Informace o hledaném řetězci jsou odesílány na server jako [hodnota pole formuláře](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="1fb89-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="1fb89-148">Můžete si je ověřit pomocí nástrojů pro vývojáře v prohlížeči nebo skvělého [nástroje Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="1fb89-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="1fb89-149">Následující obrázek ukazuje vývojářské nástroje prohlížeče Chrome:</span><span class="sxs-lookup"><span data-stu-id="1fb89-149">The image below shows the Chrome browser Developer tools:</span></span>

![Karta síť Vývojářské nástroje v Microsoft Edge ukazující tělo žádosti s hodnotou searchString Ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="1fb89-151">V textu žádosti vidíte parametr Search a token [XSRF](xref:security/anti-request-forgery) .</span><span class="sxs-lookup"><span data-stu-id="1fb89-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="1fb89-152">Všimněte si, jak je uvedeno v předchozím kurzu [Pomocník značek formuláře](xref:mvc/views/working-with-forms) generuje token odolný proti falšování [XSRF](xref:security/anti-request-forgery) .</span><span class="sxs-lookup"><span data-stu-id="1fb89-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="1fb89-153">Neupravujeme data, takže v metodě kontroleru nepotřebujeme token ověřit.</span><span class="sxs-lookup"><span data-stu-id="1fb89-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="1fb89-154">Vzhledem k tomu, že parametr Search je v textu žádosti, a ne v adrese URL, nemůžete zachytit informace o hledání do záložky nebo sdílet s ostatními.</span><span class="sxs-lookup"><span data-stu-id="1fb89-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="1fb89-155">Opravte to zadáním požadavku, který `HTTP GET` se má najít v souboru views/ *Movies/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1fb89-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="1fb89-156">Když teď odešlete hledání, adresa URL obsahuje řetězec vyhledávacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="1fb89-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="1fb89-157">Hledání bude také jít na `HttpGet Index` metodu Action, i když `HttpPost Index` máte metodu.</span><span class="sxs-lookup"><span data-stu-id="1fb89-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Okno prohlížeče zobrazující v adrese URL searchString = Ghost a vracené filmy, Ghostbusters a Ghostbusters 2, obsahují slovo Ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="1fb89-159">Následující kód ukazuje změnu na `form` značku:</span><span class="sxs-lookup"><span data-stu-id="1fb89-159">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="add-search-by-genre"></a><span data-ttu-id="1fb89-160">Přidat hledání podle žánru</span><span class="sxs-lookup"><span data-stu-id="1fb89-160">Add Search by genre</span></span>

<span data-ttu-id="1fb89-161">Přidejte následující `MovieGenreViewModel` třídu *modely* složky:</span><span class="sxs-lookup"><span data-stu-id="1fb89-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="1fb89-162">Model zobrazení filmového žánru bude obsahovat:</span><span class="sxs-lookup"><span data-stu-id="1fb89-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="1fb89-163">Seznam filmů.</span><span class="sxs-lookup"><span data-stu-id="1fb89-163">A list of movies.</span></span>
* <span data-ttu-id="1fb89-164">`SelectList` Obsahující seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="1fb89-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="1fb89-165">To umožňuje uživateli vybrat ze seznamu Žánr.</span><span class="sxs-lookup"><span data-stu-id="1fb89-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="1fb89-166">`MovieGenre`, který obsahuje vybraný Žánr.</span><span class="sxs-lookup"><span data-stu-id="1fb89-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="1fb89-167">`SearchString`, který obsahuje text uživatelé, zadejte do textového pole Hledat.</span><span class="sxs-lookup"><span data-stu-id="1fb89-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="1fb89-168">Nahraďte `MoviesController.cs` metodu v následujícím kódem: `Index`</span><span class="sxs-lookup"><span data-stu-id="1fb89-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="1fb89-169">Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="1fb89-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="1fb89-170">`SelectList` Žánry se vytvářejí vyvoláním různých žánrů (nechceme, aby seznam pro výběr měl duplicitní žánry).</span><span class="sxs-lookup"><span data-stu-id="1fb89-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="1fb89-171">Když uživatel vyhledá položku, vyhledávací hodnota se zachová do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="1fb89-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="1fb89-172">Přidat hledání podle žánru do zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="1fb89-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="1fb89-173">Aktualizace `Index.cshtml` se našla v *zobrazeních/filmech, a* to následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="1fb89-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="1fb89-174">Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:</span><span class="sxs-lookup"><span data-stu-id="1fb89-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="1fb89-175">V předchozím kódu `DisplayNameFor` pomocník HTML kontroluje `Title` vlastnost, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu.</span><span class="sxs-lookup"><span data-stu-id="1fb89-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="1fb89-176">Vzhledem k tomu, že je výraz lambda zkontrolován `model`, a `model.Movies[0]` nikoli vyhodnocen, neobdržíte porušení `model.Movies`přístupu, pokud `null` jsou nebo prázdné.</span><span class="sxs-lookup"><span data-stu-id="1fb89-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="1fb89-177">Při vyhodnocování výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="1fb89-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="1fb89-178">Otestujte aplikaci vyhledáním podle žánru, podle názvu filmu a pomocí obou:</span><span class="sxs-lookup"><span data-stu-id="1fb89-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Okno prohlížeče zobrazující výsledky https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="1fb89-180">[Předchozí](controller-methods-views.md)
> [Další](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="1fb89-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
