---
title: Přidání vyhledávání do aplikace ASP.NET Core MVC
author: rick-anderson
description: Ukazuje, jak přidat vyhledávání do základní aplikace ASP.NET Core MVC
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662867"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>Přidání vyhledávání do aplikace ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části přidáte funkci `Index` vyhledávání k metodě akce, která umožňuje vyhledávat filmy podle *žánru* nebo *názvu*.

Aktualizujte `Index` metodu nalezenou uvnitř *Controllers/MoviesController.cs* pomocí následujícího kódu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

První řádek metody `Index` akce vytvoří dotaz [LINQ](/dotnet/standard/using-linq) pro výběr filmů:

```csharp
var movies = from m in _context.Movie
             select m;
```

Dotaz je definován *pouze* v tomto okamžiku, **nebyl** spuštěn proti databázi.

Pokud `searchString` parametr obsahuje řetězec, dotaz na filmy se upraví tak, aby filtroval hodnotu hledaného řetězce:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Výše `s => s.Title.Contains()` uvedený kód je [Lambda výraz](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas se používají v dotazech [LINQ](/dotnet/standard/using-linq) založených na metodách jako argumenty `Contains` pro standardní metody operátoru dotazu, jako je například metoda [Where](/dotnet/api/system.linq.enumerable.where) nebo (použitá ve výše uvedeném kódu). Linq dotazy nejsou spouštěny, když jsou definovány nebo když jsou `Where`změněny voláním metody, jako je například , `Contains`nebo `OrderBy`. Spíše je odloženo spuštění dotazu.  To znamená, že vyhodnocení výrazu je zpožděno, dokud jeho realizovaná hodnota není skutečně iterována nad nebo `ToListAsync` je volána metoda. Další informace o spuštění odloženého dotazu naleznete v [tématu Spuštění dotazu](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Poznámka: Metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu c#, který je uveden výše. Rozlišování malých a velkých písmen na dotaz závisí na databázi a řazení. Na SQL [Server, Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLite, s výchozí řazení, je rozlišována malá a velká písmena.

Přejděte na adresu `/Movies/Index`. Přidejte řetězec dotazu, například `?searchString=Ghost` k adrese URL. Zobrazí se filtrované filmy.

![Zobrazení indexu](~/tutorials/first-mvc-app/search/_static/ghost.png)

Pokud změníte podpis `Index` metody tak, aby `id`byl `id` parametr pojmenován `{id}` , bude parametr odpovídat volitelnému zástupný symbol pro výchozí trasy nastavené v *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Změňte parametr `id` na a `searchString` všechny `id`výskyty změn na .

Předchozí `Index` metoda:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

`Index` Aktualizovaná `id` metoda s parametrem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Nyní můžete předat název hledání jako data trasy (segment adresy URL) namísto jako hodnotu řetězce dotazu.

![Zobrazení indexu se slovem ghost přidané do url a vrácený seznam filmů dvou filmů, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Nelze však očekávat, že uživatelé změní adresu URL pokaždé, když chtějí vyhledat film. Takže teď přidáte prvky uživatelského rozhraní, které jim pomohou filtrovat filmy. Pokud jste změnili `Index` podpis metody a otestovali, `ID` jak předat parametr vázaný na trasu, změňte jej zpět tak, aby byl parametr pojmenován `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Otevřete soubor *Zobrazení/Filmy/Index.cshtml* a `<form>` přidejte níže zvýrazněné značky:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Značka `<form>` HTML používá [pomocníka pro označení formuláře](xref:mvc/views/working-with-forms), takže při odeslání formuláře `Index` je řetězec filtru zaúčtován do akce řadiče filmů. Uložte změny a potom filtr otestujte.

![Zobrazení rejstříku se slovem ghost zadaným do textového pole filtru Nadpis](~/tutorials/first-mvc-app/search/_static/filter.png)

Neexistuje žádné `[HttpPost]` přetížení metody, `Index` jak byste mohli očekávat. Nepotřebujete ji, protože metoda nemění stav aplikace, jen filtrování dat.

Můžete přidat následující `[HttpPost] Index` metodu.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Parametr `notUsed` se používá k vytvoření `Index` přetížení pro metodu. O tom si promluvíme později v tutoriálu.

Pokud přidáte tuto metodu, vyvolávač akce by odpovídal metodě `[HttpPost] Index` a `[HttpPost] Index` metoda by se spouštěla, jak je znázorněno na obrázku níže.

![Okno prohlížeče s odpovědí aplikace Z HttpPost Index: filtr na ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

Však i v `[HttpPost]` případě, `Index` že přidáte tuto verzi metody, je omezení v tom, jak to všechno bylo implementováno. Představte si, že chcete přidat do záložek určité vyhledávání nebo chcete poslat odkaz přátelům, na které mohou kliknout, aby viděli stejný filtrovaný seznam filmů. Všimněte si, že adresa URL pro požadavek HTTP POST je stejná jako adresa URL pro požadavek GET (localhost:{PORT}/Movies/Index) – v adrese URL nejsou žádné informace o hledání. Informace o vyhledávacím řetězci jsou odesílány na server jako [hodnota pole formuláře](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Můžete ověřit, že s prohlížečem Developer tools nebo vynikající [Fiddler nástroj](https://www.telerik.com/fiddler). Na obrázku níže jsou zobrazeny nástroje pro vývojáře prohlížeče Chrome:

![Karta Síť nástrojů pro vývojáře v microsoft edge zobrazující tělo požadavku s hodnotou searchString ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Můžete vidět vyhledávací parametr a [token XSRF](xref:security/anti-request-forgery) v těle požadavku. Všimněte si, jak je uvedeno v předchozím kurzu, [pomocné s tagem formuláře](xref:mvc/views/working-with-forms) generuje token anti-padělání [XSRF.](xref:security/anti-request-forgery) Neupravujeme data, takže nepotřebujeme ověřit token v metodě kontroleru.

Vzhledem k tomu, že parametr hledání je v textu požadavku a není adresa URL, nelze zachytit, že informace o hledání záložku nebo sdílet s ostatními. Opravte to zadáním požadavku, který by měl být `HTTP GET` nalezen v souboru *Zobrazení/Filmy/Index.cshtml.*

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Nyní při odeslání hledání obsahuje adresa URL řetězec vyhledávacího dotazu. Hledání přejde také `HttpGet Index` na metodu akce, `HttpPost Index` i když máte metodu.

![Okno prohlížeče zobrazující searchString = duch v Url a filmy se vrátil, Ghostbusters a Ghostbusters 2, obsahují slovo ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

Následující značky ukazují změnu `form` značky:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Přidat hledání podle žánru

Přidejte `MovieGenreViewModel` do složky *Modely* následující třídu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Model zobrazení filmového žánru bude obsahovat:

* Seznam filmů.
* A `SelectList` obsahující seznam žánrů. To umožňuje uživateli vybrat žánr ze seznamu.
* `MovieGenre`, který obsahuje vybraný žánr.
* `SearchString`, který obsahuje text, který uživatelé zadají do vyhledávacího textového pole.

Nahraďte `Index` `MoviesController.cs` metodu v s následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

Žánry `SelectList` jsou vytvářeny promítáním odlišných žánrů (nechceme, aby náš výběrový seznam měl duplicitní žánry).

Když uživatel vyhledá položku, hodnota hledání zůstane zachována ve vyhledávacím poli.

## <a name="add-search-by-genre-to-the-index-view"></a>Přidání hledání podle žánru do zobrazení rejstříku

Aktualizace `Index.cshtml` nalezena v *Zobrazení / Filmy /* takto:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Zkontrolujte výraz lambda použitý v následujícím pomocném prvku HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

V předchozím kódu pomocník `DisplayNameFor` HTML kontroluje `Title` vlastnost, na kterou odkazuje výraz lambda, aby zjistil zobrazovaný název. Vzhledem k tomu, že výraz lambda je kontrolována spíše `model` `model.Movies`než `model.Movies[0]` vyhodnocena, neobdržíte narušení přístupu, když , , nebo jsou `null` nebo prázdné. Při vyhodnocení výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnoceny hodnoty vlastností modelu.

Otestujte aplikaci podle žánru, názvu filmu a podle obou:

![Okno prohlížeče zobrazující výsledkyhttps://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Předchozí](controller-methods-views.md)
> [další](new-field.md)
