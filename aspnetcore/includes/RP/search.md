# <a name="add-search-to-an-aspnet-core-razor-pages-app"></a>Přidání vyhledávání do aplikace ASP.NET Core Razor Pages

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto dokumentu, funkce vyhledávání je přidána na indexovou stránku, která umožňuje vyhledávání filmy podle *žánr* nebo *název*.

Aktualizovat indexovou stránku `OnGetAsync` metodu s následujícím kódem:

[!code-cshtml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/_ViewStart.cshtml)]

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

První řádek `OnGetAsync` metoda vytvoří [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) dotaz pro výběr videa:

```csharp
var movies = from m in _context.Movie
             select m;
```

Dotaz je *pouze* definované v tomto okamžiku, má **není** byly spuštěny v rámci databáze.

Pokud `searchString` parametr obsahuje řetězec, dotaz filmy je upravit tak, aby filtrování hledaný řetězec:

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` Kód je [výraz Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Výrazy lambda se používají v založených na volání metody [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) dotazuje jako argumenty pro standardní metody operátoru dotazu, jako [kde](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metoda nebo `Contains` (používá se v předchozím kódu). Dotazy LINQ nejsou provedeny, když máte definovány, nebo data jejich voláním metody (například `Where`, `Contains` nebo `OrderBy`). Místo toho provádění dotazu je odloženo. To znamená, že vyhodnocení výrazu je zpožděna, dokud není procházen jeho očekávané hodnoty nebo `ToListAsync` metoda je volána. Zobrazit [provádění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) Další informace.

**Poznámka:** [obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metodu spustíte v databázi, není v kódu jazyka C#. Rozlišování velikosti písmen u dotazu, závisí na databázi a kolace. Na serveru SQL Server `Contains` mapuje na [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLite s výchozí kolace je velká a malá písmena.

Přejděte na stránku filmy a připojte řetězec dotazu jako `?searchString=Ghost` na adresu URL (například `http://localhost:5000/Movies?searchString=Ghost`). Zobrazují se filtrované filmy.

![Index zobrazení](../../tutorials/razor-pages/search/_static/ghost.png)

Pokud na indexovou stránku se přidá následující šablonu trasy, vyhledávací řetězec lze předat jako segment adresy URL (například `http://localhost:5000/Movies/ghost`).

```cshtml
@page "{searchString?}"
```

Předcházející omezení trasy umožňuje hledání názvu jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.  `?` v `"{searchString?}"` znamená, že toto je parametr trasy nepovinný.

![Index zobrazení na mapách slovo ghost, přidá do adresy Url a vrácené film seznam dvou filmy, Ghostbusters a Ghostbusters 2](../../tutorials/razor-pages/search/_static/g2.png)

Ale nemůžete očekávat, že uživatelům změnit adresu URL pro hledání videa. V tomto kroku se přidá uživatelského rozhraní pro filtrování videa. Pokud jste přidali dané omezení trasy `"{searchString?}"`, odeberte ji.

Otevřít *Pages/Movies/Index.cshtml* a přidejte `<form>` značky v následujícím kódu zvýrazněno:

[!code-cshtml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Kód HTML `<form>` označení používá [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Když se odešle formulář, řetězec filtru posílá *stránek/filmy/Index* stránky. Uložte změny a filtr otestovat.

![Index zobrazení na mapách slovo ghost, zadaný do textového pole Název filtru](../../tutorials/razor-pages/search/_static/filter.png)

## <a name="search-by-genre"></a>Hledat podle žánru

Přidejte následující zvýrazněný vlastnosti do *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

`SelectList Genres` Obsahuje seznam žánrů. To umožňuje uživateli vybrat rozšířením podle tematických ze seznamu.

`MovieGenre` Vlastnost obsahuje konkrétní žánr vybere uživatele (například "západní").

Aktualizace `OnGetAsync` metodu s následujícím kódem:

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Následující kód je dotaz LINQ, který načte všechny žánry z databáze.

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList` Žánrů se vytvořila projekci odlišné žánrů.

<!-- BUG in OPS
Tag snippet_selectlist's start line '75' should be less than end line '29' when resolving "[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]"

There's no start line.

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]
-->

```csharp
Genres = new SelectList(await genreQuery.Distinct().ToListAsync());
```

### <a name="adding-search-by-genre"></a>Přidání vyhledávání podle žánru

Aktualizace *Index.cshtml* následujícím způsobem:

[!code-cshtml[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Otestujte aplikaci tak, že žánr, název filmu a obě.
