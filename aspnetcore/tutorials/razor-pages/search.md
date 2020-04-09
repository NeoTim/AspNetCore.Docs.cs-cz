---
title: Přidání vyhledávání na stránky ASP.NET Core Razor
author: rick-anderson
description: Ukazuje, jak přidat vyhledávání na ASP.NET Core Razor Stránky
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8228207b0f37a6923b29891ac3115dd0be115501
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667704"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a>Přidání vyhledávání na stránky ASP.NET Core Razor

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V následujících částech je přidáno vyhledávání filmů podle *žánru* nebo *názvu.*

Přidejte následující zvýrazněné vlastnosti do *stránek/filmů/index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: obsahuje text, který uživatelé zadají do vyhledávacího textového pole. `SearchString`má [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut. `[BindProperty]`váže hodnoty formuláře a řetězce dotazů se stejným názvem jako vlastnost. `(SupportsGet = true)`je vyžadována pro vazbu na požadavky GET.
* `Genres`: obsahuje seznam žánrů. `Genres`umožňuje uživateli vybrat žánr ze seznamu. `SelectList`Vyžaduje`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: obsahuje konkrétní žánr, který uživatel vybere (například "Západní").
* `Genres`a `MovieGenre` jsou použity později v tomto kurzu.

[!INCLUDE[](~/includes/bind-get.md)]

Aktualizujte `OnGetAsync` metodu stránky Index následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Dotaz je definován *pouze* v tomto okamžiku, **nebyl** spuštěn proti databázi.

Pokud `SearchString` vlastnost není null nebo prázdné, dotaz filmy je upraven tak, aby filtrovat na hledaný řetězec:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Kód `s => s.Title.Contains()` je [Lambda výraz](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty `Contains` pro standardní metody operátoru dotazu, jako je například metoda [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu). Linq dotazy nejsou provedeny, když jsou definovány nebo když jsou změněny `Where`voláním metody (například , `Contains` nebo `OrderBy`). Spíše je odloženo spuštění dotazu. To znamená, že vyhodnocení výrazu je zpožděno, dokud není `ToListAsync` jeho realizovaná hodnota iterována nad nebo je volána metoda. Další informace naleznete v [tématu Spuštění dotazu.](/dotnet/framework/data/adonet/ef/language-reference/query-execution)

> [!NOTE]
> Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka C#. Rozlišování malých a velkých písmen na dotaz závisí na databázi a řazení. Na SQL `Contains` Server, mapuje [sql like](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLite, s výchozí řazení, je rozlišována malá a velká písmena.

Přejděte na stránku Filmy a přidejte řetězec dotazu, například `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost`). Zobrazí se filtrované filmy.

![Zobrazení indexu](search/_static/ghost.png)

Pokud je na stránku Index přidána následující šablona trasy, může být `https://localhost:5001/Movies/Ghost`hledaný řetězec předán jako segment adresy URL (například ).

```cshtml
@page "{searchString?}"
```

Předchozí omezení trasy umožňuje prohledávat název jako data trasy (segment adresy URL) namísto jako hodnotu řetězce dotazu.  In `?` `"{searchString?}"` znamená, že se jedná o volitelný parametr trasy.

![Zobrazení indexu se slovem ghost přidané do url a vrácený seznam filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

ASP.NET Core runtime používá [vazbu modelu](xref:mvc/models/model-binding) `SearchString` k nastavení hodnoty`?searchString=Ghost`vlastnosti z`https://localhost:5001/Movies/Ghost`řetězce dotazu ( ) nebo dat trasy ( ). Vazba modelu nerozlišuje malá a velká písmena.

Nelze však očekávat, že uživatelé změní adresu URL a vyhledávají film. V tomto kroku je k filtrování filmů přidáno ui. Pokud jste přidali `"{searchString?}"`omezení trasy , odeberte ho.

Otevřete soubor *Pages/Movies/Index.cshtml* a `<form>` přidejte značky zvýrazněné v následujícím kódu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

Značka `<form>` HTML používá následující [pomocné síly značek](xref:mvc/views/tag-helpers/intro):

* [Pomocné s označením formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Po odeslání formuláře je řetězec filtru odeslán na stránku *Stránky/Filmy/Index* prostřednictvím řetězce dotazu.
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms#the-input-tag-helper)

Uložte změny a otestujte filtr.

![Zobrazení rejstříku se slovem ghost zadaným do textového pole filtru Nadpis](search/_static/filter.png)

## <a name="search-by-genre"></a>Vyhledávání podle žánru

Aktualizujte `OnGetAsync` metodu následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Následující kód je linq dotaz, který načte všechny žánry z databáze.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Žánry `SelectList` jsou vytvářeny promítáním odlišných žánrů.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Přidání vyhledávání podle žánru na stránku Břitva

Aktualizujte *index.cshtml* takto:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Otestujte aplikaci podle žánru, názvu filmu a podle obou.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Předchozí: Aktualizace stránek](xref:tutorials/razor-pages/da1)
> [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V následujících částech je přidáno vyhledávání filmů podle *žánru* nebo *názvu.*

Přidejte následující zvýrazněné vlastnosti do *stránek/filmů/index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: obsahuje text, který uživatelé zadají do vyhledávacího textového pole. `SearchString`má [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut. `[BindProperty]`váže hodnoty formuláře a řetězce dotazů se stejným názvem jako vlastnost. `(SupportsGet = true)`je vyžadována pro vazbu na požadavky GET.
* `Genres`: obsahuje seznam žánrů. `Genres`umožňuje uživateli vybrat žánr ze seznamu. `SelectList`Vyžaduje`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: obsahuje konkrétní žánr, který uživatel vybere (například "Západní").
* `Genres`a `MovieGenre` jsou použity později v tomto kurzu.

[!INCLUDE[](~/includes/bind-get.md)]

Aktualizujte `OnGetAsync` metodu stránky Index následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Dotaz je definován *pouze* v tomto okamžiku, **nebyl** spuštěn proti databázi.

Pokud `SearchString` vlastnost není null nebo prázdné, dotaz filmy je upraven tak, aby filtrovat na hledaný řetězec:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

Kód `s => s.Title.Contains()` je [Lambda výraz](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty `Contains` pro standardní metody operátoru dotazu, jako je například metoda [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo (použitá v předchozím kódu). Linq dotazy nejsou provedeny, když jsou definovány nebo když jsou změněny `Where`voláním metody (například , `Contains` nebo `OrderBy`). Spíše je odloženo spuštění dotazu. To znamená, že vyhodnocení výrazu je zpožděno, dokud není `ToListAsync` jeho realizovaná hodnota iterována nad nebo je volána metoda. Další informace naleznete v [tématu Spuštění dotazu.](/dotnet/framework/data/adonet/ef/language-reference/query-execution)

**Poznámka:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka C#. Rozlišování malých a velkých písmen na dotaz závisí na databázi a řazení. Na SQL `Contains` Server, mapuje [sql like](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLite, s výchozí řazení, je rozlišována malá a velká písmena.

Přejděte na stránku Filmy a přidejte řetězec dotazu, například `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost`). Zobrazí se filtrované filmy.

![Zobrazení indexu](search/_static/ghost.png)

Pokud je na stránku Index přidána následující šablona trasy, může být `https://localhost:5001/Movies/Ghost`hledaný řetězec předán jako segment adresy URL (například ).

```cshtml
@page "{searchString?}"
```

Předchozí omezení trasy umožňuje prohledávat název jako data trasy (segment adresy URL) namísto jako hodnotu řetězce dotazu.  In `?` `"{searchString?}"` znamená, že se jedná o volitelný parametr trasy.

![Zobrazení indexu se slovem ghost přidané do url a vrácený seznam filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

ASP.NET Core runtime používá [vazbu modelu](xref:mvc/models/model-binding) `SearchString` k nastavení hodnoty`?searchString=Ghost`vlastnosti z`https://localhost:5001/Movies/Ghost`řetězce dotazu ( ) nebo dat trasy ( ). Vazba modelu nerozlišuje malá a velká písmena.

Nelze však očekávat, že uživatelé změní adresu URL a vyhledávají film. V tomto kroku je k filtrování filmů přidáno ui. Pokud jste přidali `"{searchString?}"`omezení trasy , odeberte ho.

Otevřete soubor *Pages/Movies/Index.cshtml* a `<form>` přidejte značky zvýrazněné v následujícím kódu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Značka `<form>` HTML používá následující [pomocné síly značek](xref:mvc/views/tag-helpers/intro):

* [Pomocné s označením formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Po odeslání formuláře je řetězec filtru odeslán na stránku *Stránky/Filmy/Index* prostřednictvím řetězce dotazu.
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms#the-input-tag-helper)

Uložte změny a otestujte filtr.

![Zobrazení rejstříku se slovem ghost zadaným do textového pole filtru Nadpis](search/_static/filter.png)

## <a name="search-by-genre"></a>Vyhledávání podle žánru

Aktualizujte `OnGetAsync` metodu následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Následující kód je linq dotaz, který načte všechny žánry z databáze.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

Žánry `SelectList` jsou vytvářeny promítáním odlišných žánrů.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Přidání vyhledávání podle žánru na stránku Břitva

Aktualizujte *index.cshtml* takto:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Otestujte aplikaci podle žánru, názvu filmu a podle obou.
Předchozí kód používá [možnost Vybrat pomocnou položku značek](xref:mvc/views/working-with-forms#the-select-tag-helper) a pomocnou položku s opčními značkami.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Předchozí: Aktualizace stránek](xref:tutorials/razor-pages/da1)
> [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)

::: moniker-end
