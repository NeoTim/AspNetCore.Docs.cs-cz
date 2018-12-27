Zvýrazněný kód výše znázorňuje kontext databáze filmů přidávaný do [injektáž závislostí](xref:fundamentals/dependency-injection) kontejner (v *Startup.cs* souboru). `services.AddDbContext<MvcMovieContext>(options =>` Určuje databázi, do použití a připojovací řetězec. `=>` je [operátor lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator).

Otevřít *Controllers/MoviesController.cs* souboru a prozkoumání konstruktoru:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)] 

Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) vkládat kontext databáze (`MvcMovieContext `) do kontroleru. Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modely se silnými typy a @model – klíčové slovo

Dříve v tomto kurzu jste viděli, jak kontroleru můžete předat data nebo objekty zobrazení pomocí `ViewData` slovníku. `ViewData` Slovník je dynamický objekt, který představuje pohodlný způsob s pozdní vazbou k předávání informací do zobrazení.

MVC rovněž poskytuje možnost předávání silně typované objekty modelu zobrazení. Tento přístup umožňuje lepší kompilace Kontrola kódu silného typu. Generování uživatelského rozhraní mechanismus použít tento přístup (který předává model silného typu) se `MoviesController` třídy a zobrazení při vytvoření rovnou metody a zobrazení.

Zkontrolujte vygenerovaný `Details` metodu *Controllers/MoviesController.cs* souboru:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

`id` Jako data trasy, která je obecně předán parametr. Například `http://localhost:5000/movies/details/1` nastaví:

* Kontroleru `movies` kontroler (první segment adresy URL).
* Akce, která má `details` (druhý segment adresy URL).
* Id na hodnotu 1 (poslední segment adresy URL).

Můžete také předat `id` pomocí dotazu řetězec následujícím způsobem:

`http://localhost:1234/movies/details?id=1`

`id` Parametr je definován jako [typ připouštějící hodnotu Null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že je hodnota ID není k dispozici.

A [výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) předáno pracovnímu `FirstOrDefaultAsync` vyberte film entity, které odpovídají směrování dat nebo dotaz řetězcovou hodnotu.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Pokud se najde videa, instance `Movie` modelu je předán `Details` zobrazení:

```csharp
return View(movie);
   ```

Zkontrolovat obsah *Views/Movies/Details.cshtml* souboru:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Zahrnutím `@model` příkazu v horní části souboru zobrazení, můžete určit typ objektu, který očekává, že zobrazení. Při vytvoření kontroleru film, následující `@model` příkaz byl automaticky zahrnut v horní části *Details.cshtml* souboru:

```HTML
@model MvcMovie.Models.Movie
   ```

To `@model` – direktiva umožňuje přístup k video, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno. Například v *Details.cshtml* zobrazení, že kód předá jednotlivých polí filmů a `DisplayNameFor` a `DisplayFor` pomocných rutin HTML se silnými typy `Model` objektu. `Create` a `Edit` metody a zobrazení také předat `Movie` objekt modelu.

Zkontrolujte *Index.cshtml* zobrazení a `Index` metoda v filmy kontroleru. Všimněte si, jak kód vytvoří `List` objektu při volání `View` metody. Kód předá to `Movies` ze seznamu `Index` metody akce k zobrazení:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Když vytvoříte řadič filmy, generování uživatelského rozhraní automaticky zahrnuty následující `@model` příkazu v horní části *Index.cshtml* souboru:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` – Direktiva umožňuje přístup k seznamu filmy, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno. Například v *Index.cshtml* zobrazíte kód cyklicky projde filmů s `foreach` příkaz přes silného typu `Model` objektu:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Protože `Model` objektu je silně typováno (jako `IEnumerable<Movie>` objektu), každé položky ve smyčce je zadán jako `Movie`. Kromě dalších výhod, to znamená, že dostanete kompilace Kontrola kódu:
