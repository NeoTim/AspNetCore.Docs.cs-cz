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

<span data-ttu-id="215c1-101">Nyní když odešlete vyhledávání, adresa URL obsahuje hledaný řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="215c1-101">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="215c1-102">Hledání budou také moct `HttpGet Index` metodě akce, i když máte `HttpPost Index` metody.</span><span class="sxs-lookup"><span data-stu-id="215c1-102">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Okno prohlížeče zobrazující hledaný_řetězec = ghost v adresu Url a vrátí, filmy Ghostbusters a Ghostbusters 2 obsahují slovo ghost](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="215c1-104">Následující kód ukazuje změny `form` značky:</span><span class="sxs-lookup"><span data-stu-id="215c1-104">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="adding-search-by-genre"></a><span data-ttu-id="215c1-105">Přidání vyhledávání podle žánru</span><span class="sxs-lookup"><span data-stu-id="215c1-105">Adding Search by genre</span></span>

<span data-ttu-id="215c1-106">Přidejte následující `MovieGenreViewModel` třídu *modely* složky:</span><span class="sxs-lookup"><span data-stu-id="215c1-106">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="215c1-107">Model zobrazení žánr film bude obsahovat:</span><span class="sxs-lookup"><span data-stu-id="215c1-107">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="215c1-108">Seznam videa.</span><span class="sxs-lookup"><span data-stu-id="215c1-108">A list of movies.</span></span>
* <span data-ttu-id="215c1-109">A `SelectList` obsahující seznam žánrů.</span><span class="sxs-lookup"><span data-stu-id="215c1-109">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="215c1-110">To umožňuje uživateli vybrat rozšířením podle tematických ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="215c1-110">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="215c1-111">`MovieGenre`, který obsahuje vybrané žánr.</span><span class="sxs-lookup"><span data-stu-id="215c1-111">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="215c1-112">`SearchString`, který obsahuje uživatele zadejte do textového pole hledání text.</span><span class="sxs-lookup"><span data-stu-id="215c1-112">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="215c1-113">Nahradit `Index` metoda `MoviesController.cs` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="215c1-113">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="215c1-114">Následující kód je `LINQ` dotaz, který načte všechny žánry z databáze.</span><span class="sxs-lookup"><span data-stu-id="215c1-114">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="215c1-115">`SelectList` Žánrů se vytvořila projekci odlišné žánry (nechceme náš seznam select mít duplicitní žánry).</span><span class="sxs-lookup"><span data-stu-id="215c1-115">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="215c1-116">Když uživatel vyhledává položku, se uchovávají hledanou hodnotu do vyhledávacího pole.</span><span class="sxs-lookup"><span data-stu-id="215c1-116">When the user searches for the item, the search value is retained in the search box.</span></span> <span data-ttu-id="215c1-117">Pokud chcete zachovat hledané hodnotě, naplnění `SearchString` vlastnost s hledanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="215c1-117">To retain the search value,  populate the `SearchString` property with the search value.</span></span> <span data-ttu-id="215c1-118">Hodnota vyhledávání je `searchString` parametr `Index` akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="215c1-118">The search value is the `searchString` parameter for the `Index` controller action.</span></span>

```csharp
movieGenreVM.genres = new SelectList(await genreQuery.Distinct().ToListAsync())
```

## <a name="adding-search-by-genre-to-the-index-view"></a><span data-ttu-id="215c1-119">Přidání vyhledávání podle žánru do zobrazení indexu</span><span class="sxs-lookup"><span data-stu-id="215c1-119">Adding search by genre to the Index view</span></span>

<span data-ttu-id="215c1-120">Aktualizace `Index.cshtml` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="215c1-120">Update `Index.cshtml` as follows:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

<span data-ttu-id="215c1-121">Prozkoumejte výrazu lambda použít v následujících pomocné rutiny HTML:</span><span class="sxs-lookup"><span data-stu-id="215c1-121">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`
 
<span data-ttu-id="215c1-122">V předchozím kódu `DisplayNameFor` zkontroluje pomocné rutiny HTML `Title` vlastnost se odkazuje ve výrazu lambda lze zjistit název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="215c1-122">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="215c1-123">Vzhledem k tomu, že výraz lambda je zkontroloval spíše než vyhodnocen, jste neobdrželi narušení přístupu při `model`, `model.Movies`, nebo `model.Movies[0]` jsou `null` nebo je prázdný.</span><span class="sxs-lookup"><span data-stu-id="215c1-123">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="215c1-124">Při vyhodnocování výrazu lambda (například `@Html.DisplayFor(modelItem => item.Title)`), jsou vyhodnocovány hodnoty vlastností modelu.</span><span class="sxs-lookup"><span data-stu-id="215c1-124">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="215c1-125">Otestujte aplikaci tak, že žánr, název filmu a obě.</span><span class="sxs-lookup"><span data-stu-id="215c1-125">Test the app by searching by genre, by movie title, and by both.</span></span>
