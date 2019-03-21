---
ms.openlocfilehash: 0a9b5bc4a91607306a9f4a23756a740366b24933
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58207970"
---
<!--
[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](~/tutorials/first-mvc-app/search/_static/ghost.png)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

Nyní když odešlete vyhledávání, adresa URL obsahuje hledaný řetězec dotazu. Hledání budou také moct `HttpGet Index` metodě akce, i když máte `HttpPost Index` metody.

![Okno prohlížeče zobrazující hledaný_řetězec = ghost v adresu Url a vrátí, filmy Ghostbusters a Ghostbusters 2 obsahují slovo ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

Následující kód ukazuje změny `form` značky:

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a>Přidání vyhledávání podle žánru

Přidejte následující `MovieGenreViewModel` třídu *modely* složky:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

Model zobrazení žánr film bude obsahovat:

* Seznam videa.
* A `SelectList` obsahující seznam žánrů. To umožňuje uživateli vybrat rozšířením podle tematických ze seznamu.
* `MovieGenre`, který obsahuje vybrané žánr.
* `SearchString`, který obsahuje uživatele zadejte do textového pole hledání text.

Nahradit `Index` metoda `MoviesController.cs` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

`SelectList` Žánrů se vytvořila projekci odlišné žánry (nechceme náš seznam select mít duplicitní žánry).

Když uživatel vyhledává položku, se uchovávají hledanou hodnotu do vyhledávacího pole. Pokud chcete zachovat hledané hodnotě, naplnění `SearchString` vlastnost s hledanou hodnotu. Hodnota vyhledávání je `searchString` parametr `Index` akce kontroleru.

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
```

## <a name="adding-search-by-genre-to-the-index-view"></a>Přidání vyhledávání podle žánru do zobrazení indexu

Aktualizace `Index.cshtml` následujícím způsobem:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`
 
V předchozím kódu `DisplayNameFor` zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení. Vzhledem k tomu, že výraz lambda je zkontroloval spíše než vyhodnocen, jste neobdrželi narušení přístupu při `model`, `model.Movies`, nebo `model.Movies[0]` jsou `null` nebo je prázdný. Při vyhodnocování výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.

Otestujte aplikaci tak, že žánr, název filmu a obě.
