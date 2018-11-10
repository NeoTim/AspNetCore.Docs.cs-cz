<span data-ttu-id="78707-101">Zvýrazněný kód výše znázorňuje kontext databáze filmů přidávaný do [injektáž závislostí](xref:fundamentals/dependency-injection) kontejner (v *Startup.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="78707-101">The highlighted code above shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container (In the *Startup.cs* file).</span></span> <span data-ttu-id="78707-102">`services.AddDbContext<MvcMovieContext>(options =>` Určuje databázi, do použití a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="78707-102">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span> <span data-ttu-id="78707-103">`=>` je [operátor lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="78707-103">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span></span>

<span data-ttu-id="78707-104">Otevřít *Controllers/MoviesController.cs* souboru a prozkoumání konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="78707-104">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_1)] 

<span data-ttu-id="78707-105">Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) vkládat kontext databáze (`MvcMovieContext `) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="78707-105">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext `) into the controller.</span></span> <span data-ttu-id="78707-106">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="78707-106">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="78707-107">Modely se silnými typy a @model – klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="78707-107">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="78707-108">Dříve v tomto kurzu jste viděli, jak kontroleru můžete předat data nebo objekty zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="78707-108">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="78707-109">`ViewData` Slovník je dynamický objekt, který představuje pohodlný způsob s pozdní vazbou k předávání informací do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="78707-109">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="78707-110">MVC rovněž poskytuje možnost předávání silně typované objekty modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="78707-110">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="78707-111">Tento přístup umožňuje lepší kompilace Kontrola kódu silného typu.</span><span class="sxs-lookup"><span data-stu-id="78707-111">This strongly typed approach enables better compile-time checking of your code.</span></span> <span data-ttu-id="78707-112">Generování uživatelského rozhraní mechanismus použít tento přístup (který předává model silného typu) se `MoviesController` třídy a zobrazení při vytvoření rovnou metody a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="78707-112">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="78707-113">Zkontrolujte vygenerovaný `Details` metodu *Controllers/MoviesController.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="78707-113">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MoviesController.cs?name=snippet_details)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_details)]

::: moniker-end


<span data-ttu-id="78707-114">`id` Jako data trasy, která je obecně předán parametr.</span><span class="sxs-lookup"><span data-stu-id="78707-114">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="78707-115">Například `http://localhost:5000/movies/details/1` nastaví:</span><span class="sxs-lookup"><span data-stu-id="78707-115">For example `http://localhost:5000/movies/details/1` sets:</span></span>

* <span data-ttu-id="78707-116">Kontroleru `movies` kontroler (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="78707-116">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="78707-117">Akce, která má `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="78707-117">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="78707-118">Id na hodnotu 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="78707-118">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="78707-119">Můžete také předat `id` pomocí dotazu řetězec následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="78707-119">You can also pass in the `id` with a query string as follows:</span></span>

`http://localhost:1234/movies/details?id=1`

<span data-ttu-id="78707-120">`id` Parametr je definován jako [typ připouštějící hodnotu Null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že je hodnota ID není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="78707-120">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>



::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78707-121">A [výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) předáno pracovnímu `FirstOrDefaultAsync` vyberte film entity, které odpovídají směrování dat nebo dotaz řetězcovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78707-121">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.ID == id);
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="78707-122">A [výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) předáno pracovnímu `SingleOrDefaultAsync` vyberte film entity, které odpovídají směrování dat nebo dotaz řetězcovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="78707-122">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `SingleOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .SingleOrDefaultAsync(m => m.ID == id);
```

::: moniker-end



<span data-ttu-id="78707-123">Pokud se najde videa, instance `Movie` modelu je předán `Details` zobrazení:</span><span class="sxs-lookup"><span data-stu-id="78707-123">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="78707-124">Zkontrolovat obsah *Views/Movies/Details.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="78707-124">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="78707-125">Zahrnutím `@model` příkazu v horní části souboru zobrazení, můžete určit typ objektu, který očekává, že zobrazení.</span><span class="sxs-lookup"><span data-stu-id="78707-125">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="78707-126">Při vytvoření kontroleru video Visual Studio automaticky zahrnuty následující `@model` příkazu v horní části *Details.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="78707-126">When you created the movie controller, Visual Studio automatically included the following `@model` statement at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="78707-127">To `@model` – direktiva umožňuje přístup k video, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno.</span><span class="sxs-lookup"><span data-stu-id="78707-127">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="78707-128">Například v *Details.cshtml* zobrazení, že kód předá jednotlivých polí filmů a `DisplayNameFor` a `DisplayFor` pomocných rutin HTML se silnými typy `Model` objektu.</span><span class="sxs-lookup"><span data-stu-id="78707-128">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="78707-129">`Create` a `Edit` metody a zobrazení také předat `Movie` objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="78707-129">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="78707-130">Zkontrolujte *Index.cshtml* zobrazení a `Index` metoda v filmy kontroleru.</span><span class="sxs-lookup"><span data-stu-id="78707-130">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="78707-131">Všimněte si, jak kód vytvoří `List` objektu při volání `View` metody.</span><span class="sxs-lookup"><span data-stu-id="78707-131">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="78707-132">Kód předá to `Movies` ze seznamu `Index` metody akce k zobrazení:</span><span class="sxs-lookup"><span data-stu-id="78707-132">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="78707-133">Když vytvoříte řadič filmy, generování uživatelského rozhraní automaticky zahrnuty následující `@model` příkazu v horní části *Index.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="78707-133">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="78707-134">`@model` – Direktiva umožňuje přístup k seznamu filmy, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno.</span><span class="sxs-lookup"><span data-stu-id="78707-134">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="78707-135">Například v *Index.cshtml* zobrazíte kód cyklicky projde filmů s `foreach` příkaz přes silného typu `Model` objektu:</span><span class="sxs-lookup"><span data-stu-id="78707-135">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="78707-136">Protože `Model` objektu je silně typováno (jako `IEnumerable<Movie>` objektu), každé položky ve smyčce je zadán jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="78707-136">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="78707-137">Kromě dalších výhod, to znamená, že dostanete kompilace Kontrola kódu:</span><span class="sxs-lookup"><span data-stu-id="78707-137">Among other benefits, this means that you get compile-time checking of the code:</span></span>
