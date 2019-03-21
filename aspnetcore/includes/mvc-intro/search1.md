---
ms.openlocfilehash: 1c693712cc39c0e01ad19409bd10bc5e24737b3a
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208493"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>Přidání vyhledávání do aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části můžete přidat možnosti vyhledávání do `Index` metody akce, která umožňuje hledat filmy podle *žánr* nebo *název*.

Aktualizace `Index` metodu s následujícím kódem:
<!--
[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]
-->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

První řádek `Index` vytvoří metodu akce [LINQ](/dotnet/standard/using-linq) dotaz pro výběr videa:

```csharp
var movies = from m in _context.Movie
             select m;
```

Dotaz je *pouze* definované v tomto okamžiku, má **není** byly spuštěny v rámci databáze.

Pokud `searchString` parametr obsahuje řetězec, dotaz filmy je upravit tak, aby filtrování na základě hodnoty hledaný řetězec:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

`s => s.Title.Contains()` Je výše uvedený kód [výraz Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Výrazy lambda se používají v založených na volání metody [LINQ](/dotnet/standard/using-linq) dotazuje jako argumenty pro standardní metody operátoru dotazu, jako [kde](/dotnet/api/system.linq.enumerable.where) metoda nebo `Contains` (používá se ve výše uvedeném kódu). Dotazy LINQ nejsou provedeny, když máte definovány, nebo data jejich voláním metody `Where`, `Contains` nebo `OrderBy`. Místo toho provádění dotazu je odloženo.  To znamená, že vyhodnocení výrazu je odloženo jeho očekávané hodnoty ve skutečnosti procházen nebo `ToListAsync` metoda je volána. Další informace o odložený dotaz, naleznete v tématu [provádění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Poznámka: [Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda je spuštěna na databáze, není ve výše uvedeném kódu c#. Rozlišování velikosti písmen u dotazu, závisí na databázi a kolace. Na serveru SQL Server [obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapuje na [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLite s výchozí kolace je velká a malá písmena.

Přejděte na adresu `/Movies/Index`. Připojte řetězec dotazu jako `?searchString=Ghost` na adresu URL. Zobrazují se filtrované filmy.

![Index zobrazení](~/tutorials/first-mvc-app/search/_static/ghost.png)

Pokud se změní podpis `Index` metoda může mít parametr s názvem `id`, `id` bude odpovídat nepovinný parametr `{id}` zástupný symbol pro výchozí trasy sady v *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]
