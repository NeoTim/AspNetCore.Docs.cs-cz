---
title: Část 6, přidání hledání do ASP.NET Core Razor stránek
author: rick-anderson
description: Část 6 série kurzů na Razor stránkách.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8c5fba8596f401f1232e8dcb4659041c2a413151
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652569"
---
# <a name="part-6-add-search-to-aspnet-core-razor-pages"></a>Část 6, přidání hledání do ASP.NET Core Razor stránek

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V následujících částech se přidají hledání filmů podle *žánru* nebo *názvu* .

Přidejte následující zvýrazněné vlastnosti na *stránky/filmy/index. cshtml. cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: obsahuje text, který uživatel zadá do textového pole Hledat. `SearchString`má [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut. `[BindProperty]`vytvoří vazby na hodnoty formuláře a řetězce dotazů se stejným názvem jako má vlastnost. `(SupportsGet = true)`se vyžaduje pro vazbu na požadavky GET.
* `Genres`: obsahuje seznam žánrů. `Genres`umožňuje uživateli vybrat ze seznamu Žánr. `SelectList`nutné`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: obsahuje konkrétní Žánr, kterou uživatel vybere (například "Western").
* `Genres`a `MovieGenre` používají se později v tomto kurzu.

[!INCLUDE[](~/includes/bind-get.md)]

Aktualizujte metodu stránky indexu `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Dotaz je definován *pouze* v tomto okamžiku **, nebyl proto** spuštěn proti databázi.

Pokud `SearchString` vlastnost nemá hodnotu null nebo je prázdná, dotaz na filmy se upraví tak, aby se vyfiltroval v hledaném řetězci:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()`Kód je [výraz lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Výrazy lambda se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je například metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo `Contains` (použitá v předchozím kódu). Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody (například `Where` `Contains` nebo `OrderBy` ). Místo toho je provádění dotazu odloženo. To znamená, že vyhodnocení výrazu je zpožděno, dokud se jeho realizovaná hodnota neopakuje nebo je `ToListAsync` volána metoda. Další informace najdete v tématu [spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

> [!NOTE]
> Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka C#. Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci. Na SQL Server se `Contains` mapuje na [SQL jako](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje malá a velká písmena. V SQLite s výchozí kolací rozlišuje velká a malá písmena.

Přejděte na stránku filmy a přidejte řetězec dotazu, například `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost` ). Zobrazí se filtrované filmy.

![Zobrazení indexu](search/_static/ghost.png)

Pokud je na stránku indexu přidána následující šablona trasy, hledaný řetězec může být předán jako segment adresy URL (například `https://localhost:5001/Movies/Ghost` ).

```cshtml
@page "{searchString?}"
```

Předchozí omezení trasy umožňuje prohledávat název jako data směrování (segment adresy URL) místo jako hodnotu řetězce dotazu.  V nástroji se `?` `"{searchString?}"` označuje jako volitelný parametr trasy.

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

Modul runtime ASP.NET Core používá [vazbu modelu](xref:mvc/models/model-binding) k nastavení hodnoty `SearchString` vlastnosti z řetězce dotazu ( `?searchString=Ghost` ) nebo dat Route ( `https://localhost:5001/Movies/Ghost` ). Vazba modelu nerozlišuje velká a malá písmena.

Nemůžete ale očekávat, že uživatelé upraví adresu URL pro hledání videa. V tomto kroku se přidají uživatelské rozhraní pro filtrování filmů. Pokud jste přidali omezení trasy `"{searchString?}"` , odeberte ho.

Otevřete soubor *Pages/Movies/index. cshtml* a přidejte kód `<form>` zvýrazněný v následujícím kódu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

Značka jazyka HTML `<form>` používá následující [pomocníky značek](xref:mvc/views/tag-helpers/intro):

* [Pomocná pomůcka značek formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper) Při odeslání formuláře se řetězec filtru pošle na stránku *stránky, filmy/indexování* prostřednictvím řetězce dotazu.
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms#the-input-tag-helper)

Uložte změny a otestujte filtr.

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](search/_static/filter.png)

## <a name="search-by-genre"></a>Hledat podle žánru

Aktualizujte `OnGetAsync` metodu následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Následující kód je dotaz LINQ, který načte všechny žánry z databáze.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Žánry jsou vytvořeny vyvoláním různých žánrů.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Přidat hledání podle žánru na Razor stránku

Aktualizujte *index. cshtml* následujícím způsobem:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Otestujte aplikaci tak, že vyhledáte podle žánru, podle názvu filmu a pomocí obou.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Předchozí: aktualizace stránek](xref:tutorials/razor-pages/da1) 
>  [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

V následujících částech se přidají hledání filmů podle *žánru* nebo *názvu* .

Přidejte následující zvýrazněné vlastnosti na *stránky/filmy/index. cshtml. cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: obsahuje text, který uživatel zadá do textového pole Hledat. `SearchString`má [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atribut. `[BindProperty]`vytvoří vazby na hodnoty formuláře a řetězce dotazů se stejným názvem jako má vlastnost. `(SupportsGet = true)`se vyžaduje pro vazbu na požadavky GET.
* `Genres`: obsahuje seznam žánrů. `Genres`umožňuje uživateli vybrat ze seznamu Žánr. `SelectList`nutné`using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: obsahuje konkrétní Žánr, kterou uživatel vybere (například "Western").
* `Genres`a `MovieGenre` používají se později v tomto kurzu.

[!INCLUDE[](~/includes/bind-get.md)]

Aktualizujte metodu stránky indexu `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

První řádek `OnGetAsync` metody vytvoří dotaz [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) pro výběr filmů:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

Dotaz je definován *pouze* v tomto okamžiku **, nebyl proto** spuštěn proti databázi.

Pokud `SearchString` vlastnost nemá hodnotu null nebo je prázdná, dotaz na filmy se upraví tak, aby se vyfiltroval v hledaném řetězci:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

`s => s.Title.Contains()`Kód je [výraz lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Výrazy lambda se používají v dotazech [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je například metoda [WHERE](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) nebo `Contains` (použitá v předchozím kódu). Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody (například `Where` `Contains` nebo `OrderBy` ). Místo toho je provádění dotazu odloženo. To znamená, že vyhodnocení výrazu je zpožděno, dokud se jeho realizovaná hodnota neopakuje nebo je `ToListAsync` volána metoda. Další informace najdete v tématu [spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution) .

**Poznámka:** Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka C#. Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci. Na SQL Server se `Contains` mapuje na [SQL jako](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje malá a velká písmena. V SQLite s výchozí kolací rozlišuje velká a malá písmena.

Přejděte na stránku filmy a přidejte řetězec dotazu, například `?searchString=Ghost` na adresu URL (například `https://localhost:5001/Movies?searchString=Ghost` ). Zobrazí se filtrované filmy.

![Zobrazení indexu](search/_static/ghost.png)

Pokud je na stránku indexu přidána následující šablona trasy, hledaný řetězec může být předán jako segment adresy URL (například `https://localhost:5001/Movies/Ghost` ).

```cshtml
@page "{searchString?}"
```

Předchozí omezení trasy umožňuje prohledávat název jako data směrování (segment adresy URL) místo jako hodnotu řetězce dotazu.  V nástroji se `?` `"{searchString?}"` označuje jako volitelný parametr trasy.

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](search/_static/g2.png)

Modul runtime ASP.NET Core používá [vazbu modelu](xref:mvc/models/model-binding) k nastavení hodnoty `SearchString` vlastnosti z řetězce dotazu ( `?searchString=Ghost` ) nebo dat Route ( `https://localhost:5001/Movies/Ghost` ). Vazba modelu nerozlišuje velká a malá písmena.

Nemůžete ale očekávat, že uživatelé upraví adresu URL pro hledání videa. V tomto kroku se přidají uživatelské rozhraní pro filtrování filmů. Pokud jste přidali omezení trasy `"{searchString?}"` , odeberte ho.

Otevřete soubor *Pages/Movies/index. cshtml* a přidejte kód `<form>` zvýrazněný v následujícím kódu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

Značka jazyka HTML `<form>` používá následující [pomocníky značek](xref:mvc/views/tag-helpers/intro):

* [Pomocná pomůcka značek formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper) Při odeslání formuláře se řetězec filtru pošle na stránku *stránky, filmy/indexování* prostřednictvím řetězce dotazu.
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms#the-input-tag-helper)

Uložte změny a otestujte filtr.

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](search/_static/filter.png)

## <a name="search-by-genre"></a>Hledat podle žánru

Aktualizujte `OnGetAsync` metodu následujícím kódem:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

Následující kód je dotaz LINQ, který načte všechny žánry z databáze.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

`SelectList`Žánry jsou vytvořeny vyvoláním různých žánrů.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Přidat hledání podle žánru na Razor stránku

Aktualizujte *index. cshtml* následujícím způsobem:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Otestujte aplikaci tak, že vyhledáte podle žánru, podle názvu filmu a pomocí obou.
Předchozí kód používá pomocníka pro [výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper) a pomocníka značek možností.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Předchozí: aktualizace stránek](xref:tutorials/razor-pages/da1) 
>  [Další: Přidání nového pole](xref:tutorials/razor-pages/new-field)

::: moniker-end
