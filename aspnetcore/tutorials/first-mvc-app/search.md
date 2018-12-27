---
title: Přidání vyhledávání do aplikace ASP.NET Core MVC
author: rick-anderson
description: Ukazuje, jak přidat hledání základní aplikaci ASP.NET Core MVC
ms.author: riande
ms.date: 12/25/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 3814acf0f1cf1572f1c30ce952df910ce40b4141
ms.sourcegitcommit: 4e87712029de2aceb1cf2c52e9e3dda8195a5b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53381826"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>Přidání vyhledávání do aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části můžete přidat možnosti vyhledávání do `Index` metody akce, která umožňuje hledat filmy podle *žánr* nebo *název*.

Aktualizace `Index` metodu s následujícím kódem:

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

Poznámka: [Obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) metoda je spuštěna na databáze, není ve výše uvedeném kódu c#. Rozlišování velikosti písmen u dotazu, závisí na databázi a kolace. Na serveru SQL Server [obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapuje na [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), což je malá a velká písmena. V SQLlite s výchozí kolace je velká a malá písmena.

Přejděte na adresu `/Movies/Index`. Připojte řetězec dotazu jako `?searchString=Ghost` na adresu URL. Zobrazují se filtrované filmy.

![Index zobrazení](~/tutorials/first-mvc-app/search/_static/ghost.png)

Pokud se změní podpis `Index` metoda může mít parametr s názvem `id`, `id` bude odpovídat nepovinný parametr `{id}` zástupný symbol pro výchozí trasy sady v *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Změňte parametr `id` a všechny výskyty `searchString` změnit na `id`.

Předchozí `Index` metody:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

Aktualizovaný `Index` metodu s `id` parametr:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

Nyní lze předat název vyhledávání jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.

![Index zobrazení na mapách slovo ghost, přidá do adresy Url a vrácené film seznam dvou filmy, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Ale nemůžete očekávat, že uživatelům změnit adresu URL pokaždé, když chtějí hledat videa. Takže teď přidáte prvky uživatelského rozhraní, aby to pomohl ostatním filtrovat videa. Pokud jste změnili podpis `Index` metody testování jak předat trasy vázané `ID` parametr, změnit zpět tak, že vyžaduje parametr s názvem `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

Otevřít *Views/Movies/Index.cshtml* a přidejte `<form>` značek, jejichž přehled najdete níže:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Kód HTML `<form>` označení používá [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms), takže při odeslání formuláře řetězec filtru je odeslána do `Index` akce kontroleru videa. Uložte změny a pak test filtru.

![Index zobrazení na mapách slovo ghost, zadaný do textového pole Název filtru](~/tutorials/first-mvc-app/search/_static/filter.png)

Neexistuje žádná `[HttpPost]` přetížení `Index` metody, jako jste očekávali. Není nutné, protože metoda není změněn stav aplikace, stačí filtrování dat.

Můžete přidat následující `[HttpPost] Index` metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` Parametr se používá k vytvoření přetížení pro `Index` metody. Budeme mluvit o, který později v tomto kurzu.

Pokud chcete přidat tuto metodu, by odpovídala původce volání akce `[HttpPost] Index` metody a `[HttpPost] Index` spustili byste metodu, jak je znázorněno na následujícím obrázku.

![Okno prohlížeče se odezvy aplikací z indexu HttpPost: filtr na ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

Ale i v případě, že přidáte to `[HttpPost]` verzi `Index` metoda, existuje omezení v tom, jak to vše implementován. Představte si, že chcete konkrétní hledání (záložky) nebo chcete poslat odkaz s přáteli, mohou kliknout, chcete-li zobrazit stejné filtrovaný seznam videa. Všimněte si, že adresa URL pro odeslání požadavku HTTP POST je stejný jako adresu URL pro požadavek na získání (localhost:xxxxx/filmy/Index) – není k dispozici žádné informace o vyhledávání v adrese URL. Vyhledávací řetězec informace jsou odeslány na server jako [tvoří pole hodnota](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Ověřte, že pomocí nástroje pro vývojáře v prohlížeči nebo vynikající [nástroj Fiddler](http://www.telerik.com/fiddler). Následující obrázek ukazuje Developer tools pro prohlížeč Chrome:

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazuje text požadavku s hodnotou hledaný_řetězec ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Parametr hledání můžete zobrazit a [XSRF](xref:security/anti-request-forgery) tokenu v textu požadavku. Všimněte si, jak je uvedeno v předchozím kurzu [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms) generuje [XSRF](xref:security/anti-request-forgery) token proti padělání. Jsme nejsou úpravu dat, proto nepotřebujeme ověřit token v metodě kontroleru.

Protože parametr hledání v textu požadavku a nikoliv adresu URL, nelze zachytit těchto hledání informací na záložku nebo sdílet s ostatními. Oprava to tak, že zadáte požadavek by měl být `HTTP GET`:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Nyní když odešlete vyhledávání, adresa URL obsahuje hledaný řetězec dotazu. Hledání budou také moct `HttpGet Index` metodě akce, i když máte `HttpPost Index` metody.

![Okno prohlížeče zobrazující hledaný_řetězec = ghost v adresu Url a vrátí, filmy Ghostbusters a Ghostbusters 2 obsahují slovo ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

Následující kód ukazuje změny `form` značky:

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="add-search-by-genre"></a>Přidat hledání podle žánru

Přidejte následující `MovieGenreViewModel` třídu *modely* složky:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Model zobrazení žánr film bude obsahovat:

   * Seznam videa.
   * A `SelectList` obsahující seznam žánrů. To umožňuje uživateli vybrat rozšířením podle tematických ze seznamu.
   * `MovieGenre`, který obsahuje vybrané žánr.
   * `SearchString`, který obsahuje uživatele zadejte do textového pole hledání text.

Nahradit `Index` metoda `MoviesController.cs` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

`SelectList` Žánrů se vytvořila projekci odlišné žánry (nechceme náš seznam select mít duplicitní žánry).

Když uživatel vyhledává položku, se uchovávají hledanou hodnotu do vyhledávacího pole.

## <a name="add-search-by-genre-to-the-index-view"></a>Přidat hledání podle žánru do zobrazení indexu

Aktualizace `Index.cshtml` následujícím způsobem:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

V předchozím kódu `DisplayNameFor` zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení. Vzhledem k tomu, že výraz lambda je zkontroloval spíše než vyhodnocen, jste neobdrželi narušení přístupu při `model`, `model.Movies`, nebo `model.Movies[0]` jsou `null` nebo je prázdný. Při vyhodnocování výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.

Otestujte aplikaci tak, že žánr, název filmu a obě:

![Výsledky zobrazení okna prohlížeče https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Předchozí](controller-methods-views.md)
> [další](new-field.md)  
