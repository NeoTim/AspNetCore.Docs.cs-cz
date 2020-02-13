---
title: Přidání vyhledávání do aplikace ASP.NET Core MVC
author: rick-anderson
description: Ukazuje, jak přidat hledání do základní ASP.NET Core aplikace MVC.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171640"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>Přidání vyhledávání do aplikace ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části přidáte funkci hledání do metody `Index` akce, která umožňuje hledat filmy podle *žánru* nebo *názvu*.

Aktualizujte metodu `Index` nalezenou v *Controllers/MoviesController. cs* pomocí následujícího kódu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

První řádek metody `Index` Action vytvoří dotaz [LINQ](/dotnet/standard/using-linq) pro výběr filmů:

```csharp
var movies = from m in _context.Movie
             select m;
```

Dotaz je definován *pouze* v tomto okamžiku **, nebyl proto** spuštěn proti databázi.

Pokud parametr `searchString` obsahuje řetězec, dotaz na filmy je upravený tak, aby se vyfiltroval na hodnotu hledaného řetězce:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

Výše uvedený kód `s => s.Title.Contains()` je [výraz lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Výrazy lambda se používají v dotazech [LINQ](/dotnet/standard/using-linq) založených na metodách jako argumenty standardních metod operátoru dotazu, jako je metoda [WHERE](/dotnet/api/system.linq.enumerable.where) nebo `Contains` (používá se v kódu výše). Dotazy LINQ nejsou provedeny při jejich definování nebo při jejich úpravě voláním metody, jako je `Where`, `Contains`nebo `OrderBy`. Místo toho je provádění dotazu odloženo.  To znamená, že vyhodnocení výrazu je zpožděno, dokud se na jeho realizované hodnotě neprovádí iterace nebo je volána metoda `ToListAsync`. Další informace o odložení provádění dotazů naleznete v tématu [provádění dotazů](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Poznámka: metoda [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) je spuštěna v databázi, nikoli v kódu jazyka c# zobrazeném výše. Rozlišování velkých a malých písmen na dotazu závisí na databázi a kolaci. V SQL Server [obsahuje](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) mapy [jako SQL](/sql/t-sql/language-elements/like-transact-sql), což rozlišuje velká a malá písmena. V SQLite s výchozí kolací rozlišuje velká a malá písmena.

Přejděte na adresu `/Movies/Index`. Přidejte do adresy URL řetězec dotazu, například `?searchString=Ghost`. Zobrazí se filtrované filmy.

![Zobrazení indexu](~/tutorials/first-mvc-app/search/_static/ghost.png)

Pokud změníte podpis `Index` metody tak, aby měl parametr s názvem `id`, parametr `id` bude odpovídat volitelnému zástupnému `{id}` pro výchozí trasy nastavené v *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Změňte parametr na `id` a všechny výskyty `searchString` změnit na `id`.

Předchozí metoda `Index`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Aktualizovaná metoda `Index` s parametrem `id`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Nyní můžete název hledání předat jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.

![Zobrazení indexu s slovem Ghost, které bylo přidáno k adrese URL a vráceným seznamem filmů dvou filmů, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Nemůžete ale očekávat, že uživatelé můžou upravovat adresu URL pokaždé, když chtějí hledat film. Takže teď přidáte prvky uživatelského rozhraní, které jim pomůžou filtrovat filmy. Pokud jste změnili podpis metody `Index`, abyste otestovali, jak předat parametr vázaný na trase `ID`, změňte jej tak, aby přebírá parametr s názvem `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Otevřete soubor *views/Movies/index. cshtml* a přidejte `<form>` kód zvýrazněný níže:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Značka `<form>` HTML používá [pomocníka značky formuláře](xref:mvc/views/working-with-forms), takže když formulář odešlete, vystaví se řetězec filtru do akce `Index` na řadiči filmů. Uložte změny a potom filtr otestujte.

![Zobrazení indexu se slovem Ghost, které jste zadali do textového pole filtru nadpisu](~/tutorials/first-mvc-app/search/_static/filter.png)

Neexistuje žádná `[HttpPost]` přetížení metody `Index`, která by se vám mohla očekávat. Nepotřebujete ho, protože metoda nemění stav aplikace, stačí data filtrovat.

Můžete přidat následující metodu `[HttpPost] Index`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

Parametr `notUsed` slouží k vytvoření přetížení pro metodu `Index`. O tom budeme mluvit později v tomto kurzu.

Pokud přidáte tuto metodu, akce původce by odpovídala metodě `[HttpPost] Index` a metoda `[HttpPost] Index` by byla spuštěna, jak je znázorněno na následujícím obrázku.

![Okno prohlížeče s odpovědí na aplikaci z indexu HttpPost: filtrování na Ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

Nicméně i v případě, že přidáte tuto `[HttpPost]` verzi metody `Index`, dojde k omezení toho, jak je to vše implementováno. Představte si, že chcete vytvořit záložku konkrétního hledání nebo chcete poslat odkaz na přátele, na které můžou kliknout, aby se zobrazil stejný filtrovaný seznam filmů. Všimněte si, že adresa URL požadavku HTTP POST je stejná jako adresa URL žádosti GET (localhost: {PORT}/video/index) – v adrese URL nejsou žádné vyhledávací informace. Informace o hledaném řetězci jsou odesílány na server jako [hodnota pole formuláře](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Můžete si je ověřit pomocí nástrojů pro vývojáře v prohlížeči nebo skvělého [nástroje Fiddler](https://www.telerik.com/fiddler). Následující obrázek ukazuje vývojářské nástroje prohlížeče Chrome:

![Karta síť Vývojářské nástroje v Microsoft Edge ukazující tělo žádosti s hodnotou searchString Ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

V textu žádosti vidíte parametr Search a token [XSRF](xref:security/anti-request-forgery) . Všimněte si, jak je uvedeno v předchozím kurzu [Pomocník značek formuláře](xref:mvc/views/working-with-forms) generuje token odolný proti falšování [XSRF](xref:security/anti-request-forgery) . Neupravujeme data, takže v metodě kontroleru nepotřebujeme token ověřit.

Vzhledem k tomu, že parametr Search je v textu žádosti, a ne v adrese URL, nemůžete zachytit informace o hledání do záložky nebo sdílet s ostatními. Tento problém vyřešíte tak, že určíte, že se má `HTTP GET` najít v souboru *views/Movies/index. cshtml* .

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Když teď odešlete hledání, adresa URL obsahuje řetězec vyhledávacího dotazu. Hledání bude také přejít na metodu `HttpGet Index` akce, i když máte `HttpPost Index` metodu.

![Okno prohlížeče zobrazující v adrese URL searchString = Ghost a vracené filmy, Ghostbusters a Ghostbusters 2, obsahují slovo Ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

Následující kód ukazuje změnu `form` značce:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Přidat hledání podle žánru

Do složky *modely* přidejte následující třídu `MovieGenreViewModel`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Model zobrazení filmového žánru bude obsahovat:

* Seznam filmů.
* `SelectList` obsahující seznam žánrů. To umožňuje uživateli vybrat ze seznamu Žánr.
* `MovieGenre`, který obsahuje vybraný Žánr.
* `SearchString`, která obsahuje text uživatelé, zadejte do textového pole Hledat.

Metodu `Index` v `MoviesController.cs` nahraďte následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

`SelectList` žánrů se vytvářejí tak, že prochází jednotlivé žánry (nechceme, aby seznam pro výběr měl duplicitní žánry).

Když uživatel vyhledá položku, vyhledávací hodnota se zachová do vyhledávacího pole.

## <a name="add-search-by-genre-to-the-index-view"></a>Přidat hledání podle žánru do zobrazení indexu

Aktualizace `Index.cshtml` nalezena v *zobrazeních/filmech/* následujícím způsobem:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Prověřte výraz lambda použitý v následujícím Pomocníkovi HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

V předchozím kódu Pomocník pro `DisplayNameFor` HTML kontroluje vlastnost `Title`, na kterou se odkazuje ve výrazu lambda pro určení zobrazovaného názvu. Vzhledem k tomu, že je výraz lambda zkontrolován, nikoli vyhodnocen, neobdržíte porušení přístupu, když `model`, `model.Movies`nebo `model.Movies[0]` jsou `null` nebo prázdné. Při vyhodnocování výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`) jsou vyhodnocovány hodnoty vlastností modelu.

Otestujte aplikaci vyhledáním podle žánru, podle názvu filmu a pomocí obou:

![Okno prohlížeče zobrazující výsledky https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Předchozí](controller-methods-views.md)
> [Další](new-field.md)
