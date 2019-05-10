---
title: Přidání vyhledávání do ASP.NET Core Razor Pages
author: rick-anderson
description: Ukazuje, jak přidat vyhledávací technologie ASP.NET Core Razor Pages
ms.author: riande
ms.date: 12/3/2018
uid: tutorials/razor-pages/search
ms.openlocfilehash: d0ce13e87c3f5e66008f308f6258403ea37b8847
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899514"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a>Přidání vyhledávání do ASP.NET Core Razor Pages

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

V následujících částech hledání filmy podle *žánr* nebo *název* je přidána.

Přidejte následující zvýrazněný vlastnosti do *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: obsahuje uživatele zadejte do textového pole hledání text. `SearchString` je upravena pomocí [ `[BindProperty]` ](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut. `[BindProperty]` vytvoří vazbu hodnot formuláře a řetězce dotazu se stejným názvem jako vlastnost. `(SupportsGet = true)` je potřebná pro svázání pro požadavky GET.
* `Genres`: obsahuje seznam žánrů. `Genres` Umožňuje uživateli vybrat rozšířením podle tematických ze seznamu. `SelectList` vyžaduje `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: obsahuje konkrétní žánr vybere uživatele (například "západní").
* `Genres` a `MovieGenre` později v tomto kurzu se používá.

[!INCLUDE[](~/includes/bind-get.md)]

Aktualizovat indexovou stránku `OnGetAsync` metodu s následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

První řádek `OnGetAsync` metoda vytvoří [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) dotaz pro výběr videa:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Dotaz je *pouze* definované v tomto okamžiku, má **není** byly spuštěny v rámci databáze.

Pokud `SearchString` vlastnost není null nebo je prázdný, je filmy dotaz upravit tak, aby filtrování hledaný řetězec:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()` Kód je [výraz Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Výrazy lambda se používají v založených na volání metody [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) dotazuje jako argumenty pro standardní metody operátoru dotazu, jako [kde](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) metoda nebo `Contains` (používá se v předchozím kódu). Dotazy LINQ nejsou provedeny, když máte definovány, nebo data jejich voláním metody (například `Where`, `Contains` nebo `OrderBy`). Místo toho provádění dotazu je odloženo. To znamená, že vyhodnocení výrazu je zpožděna, dokud není procházen jeho očekávané hodnoty nebo `ToListAsync` metoda je volána. Zobrazit [provádění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) Další informace.

**Poznámka:** [Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) spuštění metody je v databázi, není C# kódu. Rozlišování velikosti písmen u dotazu, závisí na databázi a kolace. Na serveru SQL Server `Contains` mapuje na [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLite s výchozí kolace je velká a malá písmena.

Přejděte na stránku filmy a připojte řetězec dotazu jako `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost`). Zobrazují se filtrované filmy.

![Index zobrazení](search/_static/ghost.png)

Pokud na indexovou stránku se přidá následující šablonu trasy, vyhledávací řetězec lze předat jako segment adresy URL (například `https://localhost:5001/Movies/Ghost`).

```cshtml
@page "{searchString?}"
```

Předcházející omezení trasy umožňuje hledání názvu jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.  `?` v `"{searchString?}"` znamená, že toto je parametr trasy nepovinný.

![Index zobrazení na mapách slovo ghost, přidá do adresy Url a vrácené film seznam dvou filmy, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

ASP.NET Core runtime používá [vazby modelu](xref:mvc/models/model-binding) nastavit hodnotu `SearchString` vlastnost z řetězce dotazu (`?searchString=Ghost`) nebo směrovat data (`https://localhost:5001/Movies/Ghost`). Vazby modelu se nerozlišují malá a velká písmena.

Ale nemůžete očekávat, že uživatelům změnit adresu URL pro hledání videa. V tomto kroku se přidá uživatelského rozhraní pro filtrování videa. Pokud jste přidali dané omezení trasy `"{searchString?}"`, odeberte ji.

Otevřít *Pages/Movies/Index.cshtml* a přidejte `<form>` značky v následujícím kódu zvýrazněno:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Kód HTML `<form>` značky používá následující [pomocných rutin značek](xref:mvc/views/tag-helpers/intro):

* [Pomocná rutina značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Když se odešle formulář, řetězec filtru posílá *stránek/filmy/Index* stránky pomocí řetězce dotazu.
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms#the-input-tag-helper)

Uložte změny a filtr otestovat.

![Index zobrazení na mapách slovo ghost, zadaný do textového pole Název filtru](search/_static/filter.png)

## <a name="search-by-genre"></a>Hledat podle žánru

Aktualizace `OnGetAsync` metodu s následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Následující kód je dotaz LINQ, který načte všechny žánry z databáze.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList` Žánrů se vytvořila projekci odlišné žánrů.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Přidat hledání podle žánru pro stránky Razor

Aktualizace *Index.cshtml* následujícím způsobem:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Otestujte aplikaci tak, že žánr, název filmu a obě.

## <a name="additional-resources"></a>Další zdroje

* [Verzi tohoto kurzu na webu YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Předchozí: Aktualizace stránek](xref:tutorials/razor-pages/da1)
> [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)
