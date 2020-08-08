---
title: 5. část, jak aktualizovat vygenerované stránky v aplikaci ASP.NET Core
author: rick-anderson
description: 5. část série kurzů na Razor stránkách.
ms.author: riande
ms.date: 12/20/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/da1
ms.openlocfilehash: d8f1a256bfa3491ff1cc7f38a23c61beb719bd49
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022092"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="a9f88-103">5. část, jak aktualizovat vygenerované stránky v aplikaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9f88-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="a9f88-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9f88-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9f88-105">Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální.</span><span class="sxs-lookup"><span data-stu-id="a9f88-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="a9f88-106">**ReleaseDate** by mělo být **Datum vydání** (dvě slova).</span><span class="sxs-lookup"><span data-stu-id="a9f88-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="a9f88-108">Aktualizovat generovaný kód</span><span class="sxs-lookup"><span data-stu-id="a9f88-108">Update the generated code</span></span>

<span data-ttu-id="a9f88-109">Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="a9f88-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="a9f88-110">`[Column(TypeName = "decimal(18, 2)")]`Datová anotace umožňuje, aby Entity Framework Core správně namapovala `Price` na měnu v databázi.</span><span class="sxs-lookup"><span data-stu-id="a9f88-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="a9f88-111">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="a9f88-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="a9f88-112">V dalším kurzu jsou uvedena tato [Anotace](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) .</span><span class="sxs-lookup"><span data-stu-id="a9f88-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="a9f88-113">Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="a9f88-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="a9f88-114">Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.</span><span class="sxs-lookup"><span data-stu-id="a9f88-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="a9f88-115">Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a9f88-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL odkazu. http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="a9f88-117">Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Movies/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a9f88-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="a9f88-118">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="a9f88-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="a9f88-119">V předchozím kódu `AnchorTagHelper` dynamicky generuje `href` hodnotu atributu HTML ze Razor stránky (trasa je relativní), `asp-page` a ID trasy ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="a9f88-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="a9f88-120">Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="a9f88-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="a9f88-121">Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód.</span><span class="sxs-lookup"><span data-stu-id="a9f88-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="a9f88-122">Část vygenerovaného kódu HTML je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="a9f88-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="a9f88-123">Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1` ).</span><span class="sxs-lookup"><span data-stu-id="a9f88-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

### <a name="add-route-template"></a><span data-ttu-id="a9f88-124">Přidat šablonu směrování</span><span class="sxs-lookup"><span data-stu-id="a9f88-124">Add route template</span></span>

<span data-ttu-id="a9f88-125">Aktualizujte stránky upravit, podrobnosti a odstranit Razor tak, aby používaly šablonu trasy {ID: int}.</span><span class="sxs-lookup"><span data-stu-id="a9f88-125">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="a9f88-126">Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="a9f88-126">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="a9f88-127">Spusťte aplikaci a potom zobrazte zdroj.</span><span class="sxs-lookup"><span data-stu-id="a9f88-127">Run the app and then view source.</span></span> <span data-ttu-id="a9f88-128">Vygenerovaný kód HTML přidá ID do části cesty adresy URL:</span><span class="sxs-lookup"><span data-stu-id="a9f88-128">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="a9f88-129">Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="a9f88-129">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="a9f88-130">Například `http://localhost:5000/Movies/Details` vrátí chybu 404.</span><span class="sxs-lookup"><span data-stu-id="a9f88-130">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="a9f88-131">Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="a9f88-131">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="a9f88-132">Postup při testování chování `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="a9f88-132">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="a9f88-133">Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="a9f88-133">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="a9f88-134">Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="a9f88-134">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="a9f88-135">Přejděte na adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="a9f88-135">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="a9f88-136">V `@page "{id:int}"` případě direktivy není bod přerušení nikdy vyzasažen.</span><span class="sxs-lookup"><span data-stu-id="a9f88-136">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="a9f88-137">Modul směrování vrátí HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="a9f88-137">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="a9f88-138">Pomocí nástroje `@page "{id:int?}"` `OnGetAsync` vrátí metoda `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="a9f88-138">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="a9f88-139">Kontrola zpracování výjimek souběžnosti</span><span class="sxs-lookup"><span data-stu-id="a9f88-139">Review concurrency exception handling</span></span>

<span data-ttu-id="a9f88-140">Zkontrolujte `OnPostAsync` metodu v souboru *Pages/video/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a9f88-140">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="a9f88-141">Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-141">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="a9f88-142">Test `catch` bloku:</span><span class="sxs-lookup"><span data-stu-id="a9f88-142">To test the `catch` block:</span></span>

* <span data-ttu-id="a9f88-143">Nastavit zarážku na`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="a9f88-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="a9f88-144">Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="a9f88-144">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="a9f88-145">V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.</span><span class="sxs-lookup"><span data-stu-id="a9f88-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="a9f88-146">V předchozím okně prohlížeče se změny publikují ve videu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="a9f88-147">Produkční kód může chtít detekovat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="a9f88-147">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="a9f88-148">Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="a9f88-148">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="a9f88-149">Publikování a kontrola vazeb</span><span class="sxs-lookup"><span data-stu-id="a9f88-149">Posting and binding review</span></span>

<span data-ttu-id="a9f88-150">Prověřte soubory *Pages/video/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a9f88-150">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="a9f88-151">Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2` ):</span><span class="sxs-lookup"><span data-stu-id="a9f88-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="a9f88-152">`OnGetAsync`Metoda načte film z databáze a vrátí `Page` metodu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="a9f88-153">`Page`Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="a9f88-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="a9f88-154">Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu ( `@model RazorPagesMovie.Pages.Movies.EditModel` ), která umožňuje, aby byl model filmu dostupný na stránce.</span><span class="sxs-lookup"><span data-stu-id="a9f88-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="a9f88-155">Formulář pro úpravy se zobrazí s hodnotami z filmu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="a9f88-156">Když se publikuje video/úprava stránky:</span><span class="sxs-lookup"><span data-stu-id="a9f88-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="a9f88-157">Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a9f88-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="a9f88-158">`[BindProperty]`Atribut umožňuje [vazbu modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="a9f88-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="a9f88-159">Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), formulář se znovu zobrazí u odeslaných hodnot.</span><span class="sxs-lookup"><span data-stu-id="a9f88-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="a9f88-160">Pokud nedochází k žádným chybám modelu, je film uložený.</span><span class="sxs-lookup"><span data-stu-id="a9f88-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="a9f88-161">Metody HTTP GET v indexech, vytváření a odstraňování Razor stránek následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="a9f88-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="a9f88-162">Metoda HTTP POST na `OnPostAsync` Razor stránce vytvořit následuje podobný vzor jako metoda na `OnPostAsync` stránce pro úpravy Razor .</span><span class="sxs-lookup"><span data-stu-id="a9f88-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9f88-163">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a9f88-163">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a9f88-164">[Předchozí: práce s databází](xref:tutorials/razor-pages/sql) 
>  [Další: Přidat hledání](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="a9f88-164">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9f88-165">Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální.</span><span class="sxs-lookup"><span data-stu-id="a9f88-165">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="a9f88-166">**ReleaseDate** by mělo být **Datum vydání** (dvě slova).</span><span class="sxs-lookup"><span data-stu-id="a9f88-166">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="a9f88-168">Aktualizovat generovaný kód</span><span class="sxs-lookup"><span data-stu-id="a9f88-168">Update the generated code</span></span>

<span data-ttu-id="a9f88-169">Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="a9f88-169">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="a9f88-170">`[Column(TypeName = "decimal(18, 2)")]`Datová anotace umožňuje, aby Entity Framework Core správně namapovala `Price` na měnu v databázi.</span><span class="sxs-lookup"><span data-stu-id="a9f88-170">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="a9f88-171">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="a9f88-171">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="a9f88-172">V dalším kurzu jsou uvedena tato [Anotace](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) .</span><span class="sxs-lookup"><span data-stu-id="a9f88-172">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="a9f88-173">Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate").</span><span class="sxs-lookup"><span data-stu-id="a9f88-173">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="a9f88-174">Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.</span><span class="sxs-lookup"><span data-stu-id="a9f88-174">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="a9f88-175">Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="a9f88-175">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL odkazu. http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="a9f88-177">Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Movies/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a9f88-177">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="a9f88-178">[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="a9f88-178">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="a9f88-179">V předchozím kódu `AnchorTagHelper` dynamicky generuje `href` hodnotu atributu HTML ze Razor stránky (trasa je relativní), `asp-page` a ID trasy ( `asp-route-id` ).</span><span class="sxs-lookup"><span data-stu-id="a9f88-179">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="a9f88-180">Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .</span><span class="sxs-lookup"><span data-stu-id="a9f88-180">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="a9f88-181">Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód.</span><span class="sxs-lookup"><span data-stu-id="a9f88-181">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="a9f88-182">Část vygenerovaného kódu HTML je zobrazena níže:</span><span class="sxs-lookup"><span data-stu-id="a9f88-182">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="a9f88-183">Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1` ).</span><span class="sxs-lookup"><span data-stu-id="a9f88-183">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="a9f88-184">Aktualizujte stránky upravit, podrobnosti a odstranit Razor tak, aby používaly šablonu trasy {ID: int}.</span><span class="sxs-lookup"><span data-stu-id="a9f88-184">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="a9f88-185">Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"` .</span><span class="sxs-lookup"><span data-stu-id="a9f88-185">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="a9f88-186">Spusťte aplikaci a potom zobrazte zdroj.</span><span class="sxs-lookup"><span data-stu-id="a9f88-186">Run the app and then view source.</span></span> <span data-ttu-id="a9f88-187">Vygenerovaný kód HTML přidá ID do části cesty adresy URL:</span><span class="sxs-lookup"><span data-stu-id="a9f88-187">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="a9f88-188">Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="a9f88-188">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="a9f88-189">Například `http://localhost:5000/Movies/Details` vrátí chybu 404.</span><span class="sxs-lookup"><span data-stu-id="a9f88-189">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="a9f88-190">Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:</span><span class="sxs-lookup"><span data-stu-id="a9f88-190">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="a9f88-191">Postup při testování chování `@page "{id:int?}"` :</span><span class="sxs-lookup"><span data-stu-id="a9f88-191">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="a9f88-192">Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="a9f88-192">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="a9f88-193">Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="a9f88-193">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="a9f88-194">Přejděte na adresu `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="a9f88-194">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="a9f88-195">V `@page "{id:int}"` případě direktivy není bod přerušení nikdy vyzasažen.</span><span class="sxs-lookup"><span data-stu-id="a9f88-195">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="a9f88-196">Modul směrování vrátí HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="a9f88-196">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="a9f88-197">Pomocí nástroje `@page "{id:int?}"` `OnGetAsync` vrátí metoda `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="a9f88-197">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="a9f88-198">Kontrola zpracování výjimek souběžnosti</span><span class="sxs-lookup"><span data-stu-id="a9f88-198">Review concurrency exception handling</span></span>

<span data-ttu-id="a9f88-199">Zkontrolujte `OnPostAsync` metodu v souboru *Pages/video/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a9f88-199">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="a9f88-200">Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-200">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="a9f88-201">Test `catch` bloku:</span><span class="sxs-lookup"><span data-stu-id="a9f88-201">To test the `catch` block:</span></span>

* <span data-ttu-id="a9f88-202">Nastavit zarážku na`catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="a9f88-202">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="a9f88-203">Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="a9f88-203">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="a9f88-204">V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.</span><span class="sxs-lookup"><span data-stu-id="a9f88-204">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="a9f88-205">V předchozím okně prohlížeče se změny publikují ve videu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-205">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="a9f88-206">Produkční kód může chtít detekovat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="a9f88-206">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="a9f88-207">Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .</span><span class="sxs-lookup"><span data-stu-id="a9f88-207">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="a9f88-208">Publikování a kontrola vazeb</span><span class="sxs-lookup"><span data-stu-id="a9f88-208">Posting and binding review</span></span>

<span data-ttu-id="a9f88-209">Prověřte soubory *Pages/video/Edit. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a9f88-209">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="a9f88-210">Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2` ):</span><span class="sxs-lookup"><span data-stu-id="a9f88-210">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="a9f88-211">`OnGetAsync`Metoda načte film z databáze a vrátí `Page` metodu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-211">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="a9f88-212">`Page`Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="a9f88-212">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="a9f88-213">Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu ( `@model RazorPagesMovie.Pages.Movies.EditModel` ), která umožňuje, aby byl model filmu dostupný na stránce.</span><span class="sxs-lookup"><span data-stu-id="a9f88-213">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="a9f88-214">Formulář pro úpravy se zobrazí s hodnotami z filmu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-214">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="a9f88-215">Když se publikuje video/úprava stránky:</span><span class="sxs-lookup"><span data-stu-id="a9f88-215">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="a9f88-216">Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a9f88-216">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="a9f88-217">`[BindProperty]`Atribut umožňuje [vazbu modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="a9f88-217">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="a9f88-218">Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), zobrazí se formulář s odeslanými hodnotami.</span><span class="sxs-lookup"><span data-stu-id="a9f88-218">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="a9f88-219">Pokud nedochází k žádným chybám modelu, je film uložený.</span><span class="sxs-lookup"><span data-stu-id="a9f88-219">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="a9f88-220">Metody HTTP GET v indexech, vytváření a odstraňování Razor stránek následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="a9f88-220">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="a9f88-221">Metoda HTTP POST na `OnPostAsync` Razor stránce vytvořit následuje podobný vzor jako metoda na `OnPostAsync` stránce pro úpravy Razor .</span><span class="sxs-lookup"><span data-stu-id="a9f88-221">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="a9f88-222">Hledání se přidá v dalším kurzu.</span><span class="sxs-lookup"><span data-stu-id="a9f88-222">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9f88-223">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a9f88-223">Additional resources</span></span>

* [<span data-ttu-id="a9f88-224">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="a9f88-224">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="a9f88-225">[Předchozí: práce s databází](xref:tutorials/razor-pages/sql) 
>  [Další: Přidat hledání](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="a9f88-225">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
