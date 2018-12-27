# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="f4e61-101">Přidání vyhledávání do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f4e61-101">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="f4e61-102">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f4e61-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f4e61-103">V této části můžete přidat možnosti vyhledávání do `Index` metody akce, která umožňuje hledat filmy podle *žánr* nebo *název*.</span><span class="sxs-lookup"><span data-stu-id="f4e61-103">In this section you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="f4e61-104">Aktualizace `Index` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f4e61-104">Update the `Index` method with the following code:</span></span>
<!--
[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]
-->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="f4e61-105">První řádek `Index` vytvoří metodu akce [LINQ](/dotnet/standard/using-linq) dotaz pro výběr videa:</span><span class="sxs-lookup"><span data-stu-id="f4e61-105">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="f4e61-106">Dotaz je *pouze* definované v tomto okamžiku, má **není** byly spuštěny v rámci databáze.</span><span class="sxs-lookup"><span data-stu-id="f4e61-106">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="f4e61-107">Pokud `searchString` parametr obsahuje řetězec, dotaz filmy je upravit tak, aby filtrování na základě hodnoty hledaný řetězec:</span><span class="sxs-lookup"><span data-stu-id="f4e61-107">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="f4e61-108">`s => s.Title.Contains()` Je výše uvedený kód [výraz Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="f4e61-108">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="f4e61-109">Výrazy lambda se používají v založených na volání metody [LINQ](/dotnet/standard/using-linq) dotazuje jako argumenty pro standardní metody operátoru dotazu, jako [kde](/dotnet/api/system.linq.enumerable.where) metoda nebo `Contains` (používá se ve výše uvedeném kódu).</span><span class="sxs-lookup"><span data-stu-id="f4e61-109">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="f4e61-110">Dotazy LINQ nejsou provedeny, když máte definovány, nebo data jejich voláním metody `Where`, `Contains` nebo `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="f4e61-110">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="f4e61-111">Místo toho provádění dotazu je odloženo.</span><span class="sxs-lookup"><span data-stu-id="f4e61-111">Rather, query execution is deferred.</span></span>  <span data-ttu-id="f4e61-112">To znamená, že vyhodnocení výrazu je odloženo jeho očekávané hodnoty ve skutečnosti procházen nebo `ToListAsync` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="f4e61-112">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="f4e61-113">Další informace o odložený dotaz, naleznete v tématu [provádění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="f4e61-113">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="f4e61-114">Poznámka: [Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda je spuštěna na databáze, není ve výše uvedeném kódu c#.</span><span class="sxs-lookup"><span data-stu-id="f4e61-114">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="f4e61-115">Rozlišování velikosti písmen u dotazu, závisí na databázi a kolace.</span><span class="sxs-lookup"><span data-stu-id="f4e61-115">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="f4e61-116">Na serveru SQL Server [obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapuje na [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="f4e61-116">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="f4e61-117">V SQLlite s výchozí kolace je velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="f4e61-117">In SQLlite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="f4e61-118">Přejděte na adresu `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="f4e61-118">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="f4e61-119">Připojte řetězec dotazu jako `?searchString=Ghost` na adresu URL.</span><span class="sxs-lookup"><span data-stu-id="f4e61-119">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="f4e61-120">Zobrazují se filtrované filmy.</span><span class="sxs-lookup"><span data-stu-id="f4e61-120">The filtered movies are displayed.</span></span>

![Index zobrazení](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="f4e61-122">Pokud se změní podpis `Index` metoda může mít parametr s názvem `id`, `id` bude odpovídat nepovinný parametr `{id}` zástupný symbol pro výchozí trasy sady v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f4e61-122">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]
