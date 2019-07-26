---
title: Aktualizovat vygenerované stránky v aplikaci ASP.NET Core
author: rick-anderson
description: Naučte se aktualizovat vygenerované stránky v ASP.NET Core aplikaci.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: f1f69b7facf584d46248405c808e75bdd8448d2b
ms.sourcegitcommit: 051f068c78931432e030b60094c38376d64d013e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440326"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="49215-103">Aktualizovat vygenerované stránky v aplikaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49215-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="49215-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="49215-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49215-105">Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální.</span><span class="sxs-lookup"><span data-stu-id="49215-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="49215-106">**ReleaseDate** by mělo být **Datum vydání** (dvě slova).</span><span class="sxs-lookup"><span data-stu-id="49215-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="49215-108">Aktualizovat generovaný kód</span><span class="sxs-lookup"><span data-stu-id="49215-108">Update the generated code</span></span>

<span data-ttu-id="49215-109">Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="49215-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="49215-110">Datová anotace umožňuje, aby Entity Framework Core správně `Price` namapovala na měnu v databázi. `[Column(TypeName = "decimal(18, 2)")]`</span><span class="sxs-lookup"><span data-stu-id="49215-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="49215-111">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="49215-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="49215-112">[](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) V dalším kurzu jsou uvedena tato anotace.</span><span class="sxs-lookup"><span data-stu-id="49215-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="49215-113">Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="49215-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="49215-114">Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.</span><span class="sxs-lookup"><span data-stu-id="49215-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="49215-115">Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="49215-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL http://localhost:1234/Movies/Edit/5 odkazu.](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="49215-117">Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru Pages */Movies/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="49215-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="49215-118">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="49215-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="49215-119">V předchozím kódu `AnchorTagHelper` dynamicky generuje hodnotu atributu HTML `href` ze stránky Razor (trasa `asp-page`je relativní), a ID trasy (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="49215-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="49215-120">Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="49215-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="49215-121">Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód.</span><span class="sxs-lookup"><span data-stu-id="49215-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="49215-122">Část vygenerovaného kódu HTML je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="49215-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="49215-123">Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="49215-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="49215-124">Aktualizujte Razor Pages upravit, podrobnosti a odstranit, aby se použila šablona trasy {ID: int}.</span><span class="sxs-lookup"><span data-stu-id="49215-124">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="49215-125">Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="49215-125">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="49215-126">Spusťte aplikaci a potom zobrazte zdroj.</span><span class="sxs-lookup"><span data-stu-id="49215-126">Run the app and then view source.</span></span> <span data-ttu-id="49215-127">Vygenerovaný kód HTML přidá ID do části cesty adresy URL:</span><span class="sxs-lookup"><span data-stu-id="49215-127">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="49215-128">Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="49215-128">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="49215-129">Například `http://localhost:5000/Movies/Details` vrátí chybu 404.</span><span class="sxs-lookup"><span data-stu-id="49215-129">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="49215-130">Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="49215-130">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="49215-131">Postup při testování chování `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="49215-131">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="49215-132">Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="49215-132">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="49215-133">Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="49215-133">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="49215-134">Přejděte na adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="49215-134">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="49215-135">V případě `@page "{id:int}"` direktivy není bod přerušení nikdy vyzasažen.</span><span class="sxs-lookup"><span data-stu-id="49215-135">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="49215-136">Modul směrování vrátí HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="49215-136">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="49215-137">Pomocí nástroje `@page "{id:int?}"`vrátí `NotFound` metoda (HTTP 404). `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="49215-137">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="49215-138">Kontrola zpracování výjimek souběžnosti</span><span class="sxs-lookup"><span data-stu-id="49215-138">Review concurrency exception handling</span></span>

<span data-ttu-id="49215-139">Zkontrolujte metodu v souboru *Pages/video/Edit. cshtml. cs:* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="49215-139">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="49215-140">Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.</span><span class="sxs-lookup"><span data-stu-id="49215-140">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="49215-141">Test `catch` bloku:</span><span class="sxs-lookup"><span data-stu-id="49215-141">To test the `catch` block:</span></span>

* <span data-ttu-id="49215-142">Nastavit zarážku na`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="49215-142">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="49215-143">Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="49215-143">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="49215-144">V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.</span><span class="sxs-lookup"><span data-stu-id="49215-144">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="49215-145">V předchozím okně prohlížeče se změny publikují ve videu.</span><span class="sxs-lookup"><span data-stu-id="49215-145">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="49215-146">Produkční kód může chtít detekovat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="49215-146">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="49215-147">Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="49215-147">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="49215-148">Publikování a kontrola vazeb</span><span class="sxs-lookup"><span data-stu-id="49215-148">Posting and binding review</span></span>

<span data-ttu-id="49215-149">Prověřte soubory Pages/ *video/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="49215-149">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="49215-150">Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="49215-150">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="49215-151">Metoda načte film z databáze a `Page` vrátí metodu. `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="49215-151">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="49215-152">Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor. `Page`</span><span class="sxs-lookup"><span data-stu-id="49215-152">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="49215-153">Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu (`@model RazorPagesMovie.Pages.Movies.EditModel`), která umožňuje, aby byl model filmu dostupný na stránce.</span><span class="sxs-lookup"><span data-stu-id="49215-153">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="49215-154">Formulář pro úpravy se zobrazí s hodnotami z filmu.</span><span class="sxs-lookup"><span data-stu-id="49215-154">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="49215-155">Když se publikuje video/úprava stránky:</span><span class="sxs-lookup"><span data-stu-id="49215-155">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="49215-156">Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="49215-156">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="49215-157">Atribut umožňuje [vazbu modelu.](xref:mvc/models/model-binding) `[BindProperty]`</span><span class="sxs-lookup"><span data-stu-id="49215-157">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="49215-158">Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), formulář se znovu zobrazí u odeslaných hodnot.</span><span class="sxs-lookup"><span data-stu-id="49215-158">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="49215-159">Pokud nedochází k žádným chybám modelu, je film uložený.</span><span class="sxs-lookup"><span data-stu-id="49215-159">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="49215-160">Metody HTTP GET v indexech, vytváření a odstraňování stránek Razor následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="49215-160">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="49215-161">Metoda HTTP Post `OnPostAsync` na stránce vytvořit Razor následuje podobný vzor `OnPostAsync` jako metoda na stránce Upravit Razor.</span><span class="sxs-lookup"><span data-stu-id="49215-161">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49215-162">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="49215-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="49215-163">[Předchozí Práce s databází](xref:tutorials/razor-pages/sql)
> Next[: Přidat hledání](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="49215-163">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="49215-164">Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální.</span><span class="sxs-lookup"><span data-stu-id="49215-164">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="49215-165">**ReleaseDate** by mělo být **Datum vydání** (dvě slova).</span><span class="sxs-lookup"><span data-stu-id="49215-165">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="49215-167">Aktualizovat generovaný kód</span><span class="sxs-lookup"><span data-stu-id="49215-167">Update the generated code</span></span>

<span data-ttu-id="49215-168">Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="49215-168">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="49215-169">Datová anotace umožňuje, aby Entity Framework Core správně `Price` namapovala na měnu v databázi. `[Column(TypeName = "decimal(18, 2)")]`</span><span class="sxs-lookup"><span data-stu-id="49215-169">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="49215-170">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="49215-170">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="49215-171">[](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) V dalším kurzu jsou uvedena tato anotace.</span><span class="sxs-lookup"><span data-stu-id="49215-171">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="49215-172">Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="49215-172">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="49215-173">Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.</span><span class="sxs-lookup"><span data-stu-id="49215-173">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="49215-174">Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="49215-174">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL http://localhost:1234/Movies/Edit/5 odkazu.](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="49215-176">Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru Pages */Movies/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="49215-176">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="49215-177">[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML.</span><span class="sxs-lookup"><span data-stu-id="49215-177">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="49215-178">V předchozím kódu `AnchorTagHelper` dynamicky generuje hodnotu atributu HTML `href` ze stránky Razor (trasa `asp-page`je relativní), a ID trasy (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="49215-178">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="49215-179">Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="49215-179">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="49215-180">Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód.</span><span class="sxs-lookup"><span data-stu-id="49215-180">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="49215-181">Část vygenerovaného kódu HTML je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="49215-181">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="49215-182">Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="49215-182">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="49215-183">Aktualizujte Razor Pages upravit, podrobnosti a odstranit, aby se použila šablona trasy {ID: int}.</span><span class="sxs-lookup"><span data-stu-id="49215-183">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="49215-184">Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="49215-184">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="49215-185">Spusťte aplikaci a potom zobrazte zdroj.</span><span class="sxs-lookup"><span data-stu-id="49215-185">Run the app and then view source.</span></span> <span data-ttu-id="49215-186">Vygenerovaný kód HTML přidá ID do části cesty adresy URL:</span><span class="sxs-lookup"><span data-stu-id="49215-186">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="49215-187">Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="49215-187">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="49215-188">Například `http://localhost:5000/Movies/Details` vrátí chybu 404.</span><span class="sxs-lookup"><span data-stu-id="49215-188">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="49215-189">Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="49215-189">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="49215-190">Postup při testování chování `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="49215-190">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="49215-191">Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="49215-191">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="49215-192">Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="49215-192">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="49215-193">Přejděte na adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="49215-193">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="49215-194">V případě `@page "{id:int}"` direktivy není bod přerušení nikdy vyzasažen.</span><span class="sxs-lookup"><span data-stu-id="49215-194">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="49215-195">Modul směrování vrátí HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="49215-195">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="49215-196">Pomocí nástroje `@page "{id:int?}"`vrátí `NotFound` metoda (HTTP 404). `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="49215-196">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="49215-197">Kontrola zpracování výjimek souběžnosti</span><span class="sxs-lookup"><span data-stu-id="49215-197">Review concurrency exception handling</span></span>

<span data-ttu-id="49215-198">Zkontrolujte metodu v souboru *Pages/video/Edit. cshtml. cs:* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="49215-198">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="49215-199">Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.</span><span class="sxs-lookup"><span data-stu-id="49215-199">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="49215-200">Test `catch` bloku:</span><span class="sxs-lookup"><span data-stu-id="49215-200">To test the `catch` block:</span></span>

* <span data-ttu-id="49215-201">Nastavit zarážku na`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="49215-201">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="49215-202">Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="49215-202">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="49215-203">V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.</span><span class="sxs-lookup"><span data-stu-id="49215-203">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="49215-204">V předchozím okně prohlížeče se změny publikují ve videu.</span><span class="sxs-lookup"><span data-stu-id="49215-204">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="49215-205">Produkční kód může chtít detekovat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="49215-205">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="49215-206">Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="49215-206">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="49215-207">Publikování a kontrola vazeb</span><span class="sxs-lookup"><span data-stu-id="49215-207">Posting and binding review</span></span>

<span data-ttu-id="49215-208">Prověřte soubory Pages/ *video/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="49215-208">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="49215-209">Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="49215-209">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="49215-210">Metoda načte film z databáze a `Page` vrátí metodu. `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="49215-210">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="49215-211">Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor. `Page`</span><span class="sxs-lookup"><span data-stu-id="49215-211">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="49215-212">Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu (`@model RazorPagesMovie.Pages.Movies.EditModel`), která umožňuje, aby byl model filmu dostupný na stránce.</span><span class="sxs-lookup"><span data-stu-id="49215-212">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="49215-213">Formulář pro úpravy se zobrazí s hodnotami z filmu.</span><span class="sxs-lookup"><span data-stu-id="49215-213">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="49215-214">Když se publikuje video/úprava stránky:</span><span class="sxs-lookup"><span data-stu-id="49215-214">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="49215-215">Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="49215-215">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="49215-216">Atribut umožňuje [vazbu modelu.](xref:mvc/models/model-binding) `[BindProperty]`</span><span class="sxs-lookup"><span data-stu-id="49215-216">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="49215-217">Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), zobrazí se formulář s odeslanými hodnotami.</span><span class="sxs-lookup"><span data-stu-id="49215-217">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="49215-218">Pokud nedochází k žádným chybám modelu, je film uložený.</span><span class="sxs-lookup"><span data-stu-id="49215-218">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="49215-219">Metody HTTP GET v indexech, vytváření a odstraňování stránek Razor následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="49215-219">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="49215-220">Metoda HTTP Post `OnPostAsync` na stránce vytvořit Razor následuje podobný vzor `OnPostAsync` jako metoda na stránce Upravit Razor.</span><span class="sxs-lookup"><span data-stu-id="49215-220">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="49215-221">Hledání se přidá v dalším kurzu.</span><span class="sxs-lookup"><span data-stu-id="49215-221">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49215-222">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="49215-222">Additional resources</span></span>

* [<span data-ttu-id="49215-223">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="49215-223">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="49215-224">[Předchozí Práce s databází](xref:tutorials/razor-pages/sql)
> Next[: Přidat hledání](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="49215-224">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
