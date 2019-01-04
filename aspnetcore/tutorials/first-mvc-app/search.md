---
title: Přidání vyhledávání do aplikace ASP.NET Core MVC
author: rick-anderson
description: Ukazuje, jak přidat hledání základní aplikaci ASP.NET Core MVC
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 8686041c3629faf9ffc4ab766e8d78eda00740dc
ms.sourcegitcommit: e1cc4c1ef6c9e07918a609d5ad7fadcb6abe3e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53997263"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="da613-103">Přidání vyhledávání do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="da613-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="da613-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da613-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da613-105">V této části můžete přidat možnosti vyhledávání do `Index` metody akce, která umožňuje hledat filmy podle *žánr* nebo *název*.</span><span class="sxs-lookup"><span data-stu-id="da613-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="da613-106">Aktualizace `Index` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="da613-106">Update the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="da613-107">První řádek `Index` vytvoří metodu akce [LINQ](/dotnet/standard/using-linq) dotaz pro výběr videa:</span><span class="sxs-lookup"><span data-stu-id="da613-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="da613-108">Dotaz je *pouze* definované v tomto okamžiku, má **není** byly spuštěny v rámci databáze.</span><span class="sxs-lookup"><span data-stu-id="da613-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="da613-109">Pokud `searchString` parametr obsahuje řetězec, dotaz filmy je upravit tak, aby filtrování na základě hodnoty hledaný řetězec:</span><span class="sxs-lookup"><span data-stu-id="da613-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="da613-110">`s => s.Title.Contains()` Je výše uvedený kód [výraz Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="da613-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="da613-111">Výrazy lambda se používají v založených na volání metody [LINQ](/dotnet/standard/using-linq) dotazuje jako argumenty pro standardní metody operátoru dotazu, jako [kde](/dotnet/api/system.linq.enumerable.where) metoda nebo `Contains` (používá se ve výše uvedeném kódu).</span><span class="sxs-lookup"><span data-stu-id="da613-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="da613-112">Dotazy LINQ nejsou provedeny, když máte definovány, nebo data jejich voláním metody `Where`, `Contains`, nebo `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="da613-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="da613-113">Místo toho provádění dotazu je odloženo.</span><span class="sxs-lookup"><span data-stu-id="da613-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="da613-114">To znamená, že vyhodnocení výrazu je odloženo jeho očekávané hodnoty ve skutečnosti procházen nebo `ToListAsync` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="da613-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="da613-115">Další informace o odložený dotaz, naleznete v tématu [provádění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="da613-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="da613-116">Poznámka: [Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda je spuštěna na databáze, není ve výše uvedeném kódu c#.</span><span class="sxs-lookup"><span data-stu-id="da613-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="da613-117">Rozlišování velikosti písmen u dotazu, závisí na databázi a kolace.</span><span class="sxs-lookup"><span data-stu-id="da613-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="da613-118">Na serveru SQL Server [obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapuje na [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="da613-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="da613-119">V SQLlite s výchozí kolace je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="da613-119">In SQLlite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="da613-120">Přejděte na adresu `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="da613-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="da613-121">Připojte řetězec dotazu jako `?searchString=Ghost` na adresu URL.</span><span class="sxs-lookup"><span data-stu-id="da613-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="da613-122">Zobrazují se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="da613-122">The filtered movies are displayed.</span></span>

![Index zobrazení](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="da613-124">Pokud se změní podpis `Index` metoda může mít parametr s názvem `id`, `id` bude odpovídat nepovinný parametr `{id}` zástupný symbol pro výchozí trasy sady v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="da613-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="da613-125">Změňte parametr `id` a všechny výskyty `searchString` změnit na `id`.</span><span class="sxs-lookup"><span data-stu-id="da613-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="da613-126">Předchozí `Index` metody:</span><span class="sxs-lookup"><span data-stu-id="da613-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

<span data-ttu-id="da613-127">Aktualizovaný `Index` metodu s `id` parametr:</span><span class="sxs-lookup"><span data-stu-id="da613-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

<span data-ttu-id="da613-128">Nyní lze předat název vyhledávání jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="da613-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Index zobrazení na mapách slovo ghost, přidá do adresy Url a vrácené film seznam dvou filmy, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="da613-130">Ale nemůžete očekávat, že uživatelům změnit adresu URL pokaždé, když chtějí hledat videa.</span><span class="sxs-lookup"><span data-stu-id="da613-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="da613-131">Takže teď přidáte prvky uživatelského rozhraní, aby to pomohl ostatním filtrovat videa.</span><span class="sxs-lookup"><span data-stu-id="da613-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="da613-132">Pokud jste změnili podpis `Index` metody testování jak předat trasy vázané `ID` parametr, změnit zpět tak, že vyžaduje parametr s názvem `searchString`:</span><span class="sxs-lookup"><span data-stu-id="da613-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

<span data-ttu-id="da613-133">Otevřít *Views/Movies/Index.cshtml* a přidejte `<form>` značek, jejichž přehled najdete níže:</span><span class="sxs-lookup"><span data-stu-id="da613-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="da613-134">Kód HTML `<form>` označení používá [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms), takže při odeslání formuláře řetězec filtru je odeslána do `Index` akce kontroleru videa.</span><span class="sxs-lookup"><span data-stu-id="da613-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="da613-135">Uložte změny a pak test filtru.</span><span class="sxs-lookup"><span data-stu-id="da613-135">Save your changes and then test the filter.</span></span>

![Index zobrazení na mapách slovo ghost, zadaný do textového pole Název filtru](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="da613-137">Neexistuje žádná `[HttpPost]` přetížení `Index` metody, jako jste očekávali.</span><span class="sxs-lookup"><span data-stu-id="da613-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="da613-138">Není nutné, protože metoda není změněn stav aplikace, stačí filtrování dat.</span><span class="sxs-lookup"><span data-stu-id="da613-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="da613-139">Můžete přidat následující `[HttpPost] Index` metody.</span><span class="sxs-lookup"><span data-stu-id="da613-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="da613-140">`notUsed` Parametr se používá k vytvoření přetížení pro `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="da613-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="da613-141">Budeme mluvit o, který později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="da613-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="da613-142">Pokud chcete přidat tuto metodu, by odpovídala původce volání akce `[HttpPost] Index` metody a `[HttpPost] Index` spustili byste metodu, jak je znázorněno na následujícím obrázku.</span><span class="sxs-lookup"><span data-stu-id="da613-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Okno prohlížeče se odezvy aplikací z indexu HttpPost: filtr na ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="da613-144">Ale i v případě, že přidáte to `[HttpPost]` verzi `Index` metoda, existuje omezení v tom, jak to vše implementován.</span><span class="sxs-lookup"><span data-stu-id="da613-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="da613-145">Představte si, že chcete konkrétní hledání (záložky) nebo chcete poslat odkaz s přáteli, mohou kliknout, chcete-li zobrazit stejné filtrovaný seznam videa.</span><span class="sxs-lookup"><span data-stu-id="da613-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="da613-146">Všimněte si, že adresa URL pro odeslání požadavku HTTP POST je stejný jako adresu URL pro požadavek na získání (localhost:xxxxx/filmy/Index) – není k dispozici žádné informace o vyhledávání v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="da613-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="da613-147">Vyhledávací řetězec informace jsou odeslány na server jako [tvoří pole hodnota](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="da613-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="da613-148">Ověřte, že pomocí nástroje pro vývojáře v prohlížeči nebo vynikající [nástroj Fiddler](http://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="da613-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](http://www.telerik.com/fiddler).</span></span> <span data-ttu-id="da613-149">Následující obrázek ukazuje Developer tools pro prohlížeč Chrome:</span><span class="sxs-lookup"><span data-stu-id="da613-149">The image below shows the Chrome browser Developer tools:</span></span>

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazuje text požadavku s hodnotou hledaný_řetězec ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="da613-151">Parametr hledání můžete zobrazit a [XSRF](xref:security/anti-request-forgery) tokenu v textu požadavku.</span><span class="sxs-lookup"><span data-stu-id="da613-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="da613-152">Všimněte si, jak je uvedeno v předchozím kurzu [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms) generuje [XSRF](xref:security/anti-request-forgery) token proti padělání.</span><span class="sxs-lookup"><span data-stu-id="da613-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="da613-153">Jsme nejsou úpravu dat, proto nepotřebujeme ověřit token v metodě kontroleru.</span><span class="sxs-lookup"><span data-stu-id="da613-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="da613-154">Protože parametr hledání v textu požadavku a nikoliv adresu URL, nelze zachytit těchto hledání informací na záložku nebo sdílet s ostatními.</span><span class="sxs-lookup"><span data-stu-id="da613-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="da613-155">Oprava to tak, že zadáte požadavek by měl být `HTTP GET`:</span><span class="sxs-lookup"><span data-stu-id="da613-155">Fix this by specifying the request should be `HTTP GET`:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="da613-156">Nyní když odešlete vyhledávání, adresa URL obsahuje hledaný řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="da613-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="da613-157">Hledání budou také moct `HttpGet Index` metodě akce, i když máte `HttpPost Index` metody.</span><span class="sxs-lookup"><span data-stu-id="da613-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Okno prohlížeče zobrazující hledaný_řetězec = ghost v adresu Url a vrátí, filmy Ghostbusters a Ghostbusters 2 obsahují slovo ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="da613-159">Následující kód ukazuje změny `form` značky:</span><span class="sxs-lookup"><span data-stu-id="da613-159">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="add-search-by-genre"></a><span data-ttu-id="da613-160">Přidat hledání podle žánru</span><span class="sxs-lookup"><span data-stu-id="da613-160">Add Search by genre</span></span>

<span data-ttu-id="da613-161">Přidejte následující `MovieGenreViewModel` třídu *modely* složky:</span><span class="sxs-lookup"><span data-stu-id="da613-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="da613-162">Model zobrazení žánr film bude obsahovat:</span><span class="sxs-lookup"><span data-stu-id="da613-162">The movie-genre view model will contain:</span></span>

   * <span data-ttu-id="da613-163">Seznam videa.</span><span class="sxs-lookup"><span data-stu-id="da613-163">A list of movies.</span></span>
   * <span data-ttu-id="da613-164">A `SelectList` obsahující seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="da613-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="da613-165">To umožňuje uživateli vybrat rozšířením podle tematických ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="da613-165">This allows the user to select a genre from the list.</span></span>
   * <span data-ttu-id="da613-166">`MovieGenre`, který obsahuje vybrané žánr.</span><span class="sxs-lookup"><span data-stu-id="da613-166">`MovieGenre`, which contains the selected genre.</span></span>
   * <span data-ttu-id="da613-167">`SearchString`, který obsahuje uživatele zadejte do textového pole hledání text.</span><span class="sxs-lookup"><span data-stu-id="da613-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="da613-168">Nahradit `Index` metoda `MoviesController.cs` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="da613-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="da613-169">Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="da613-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="da613-170">`SelectList` Žánrů se vytvořila projekci odlišné žánry (nechceme náš seznam select mít duplicitní žánry).</span><span class="sxs-lookup"><span data-stu-id="da613-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="da613-171">Když uživatel vyhledává položku, se uchovávají hledanou hodnotu do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="da613-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="da613-172">Přidat hledání podle žánru do zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="da613-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="da613-173">Aktualizace `Index.cshtml` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="da613-173">Update `Index.cshtml` as follows:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

<span data-ttu-id="da613-174">Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:</span><span class="sxs-lookup"><span data-stu-id="da613-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="da613-175">V předchozím kódu `DisplayNameFor` zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="da613-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="da613-176">Vzhledem k tomu, že výraz lambda je zkontroloval spíše než vyhodnocen, jste neobdrželi narušení přístupu při `model`, `model.Movies`, nebo `model.Movies[0]` jsou `null` nebo je prázdný.</span><span class="sxs-lookup"><span data-stu-id="da613-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="da613-177">Při vyhodnocování výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="da613-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="da613-178">Otestujte aplikaci tak, že žánr, název filmu a obě:</span><span class="sxs-lookup"><span data-stu-id="da613-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Výsledky zobrazení okna prohlížeče https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="da613-180">[Předchozí](controller-methods-views.md)
> [další](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="da613-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>  
