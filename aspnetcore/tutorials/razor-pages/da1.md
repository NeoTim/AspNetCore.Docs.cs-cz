---
title: Aktualizace vygenerovaných stránek v aplikaci ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak aktualizovat generované stránky v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 0f6535462fe2d308825bf7289c10d2b0690cebd4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666213"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>Aktualizace vygenerovaných stránek v aplikaci ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Šálnatá filmová aplikace má dobrý začátek, ale prezentace není ideální. **Datum vydání** by mělo být **datum vydání** (dvě slova).

![Filmová aplikace otevřená v Chromu](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizace generovaného kódu

Otevřete soubor *Models/Movie.cs* a přidejte zvýrazněné řádky zobrazené v následujícím kódu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Anotace `[Column(TypeName = "decimal(18, 2)")]` dat umožňuje core entity `Price` framework správně mapovat na měnu v databázi. Další informace naleznete [v tématu Datové typy](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) je popsána v dalším kurzu. Display [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) Atribut určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

Přejděte na Stránky/filmy a najeďte na odkaz **Upravit a** podívejte se na cílovou adresu URL.

![Okno prohlížeče s myší na odkaz Upravit http://localhost:1234/Movies/Edit/5 a zobrazí se adresa URL odkazu](~/tutorials/razor-pages/da1/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značky ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Movies/Index.cshtml.*

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. V předchozím kódu `AnchorTagHelper` dynamicky generuje hodnotu `href` atributu HTML ze stránky Razor Page `asp-page`(trasa je`asp-route-id`relativní), a id trasy ( ). Další informace najdete [v tématu generování adres URL pro stránky.](xref:razor-pages/index#url-generation-for-pages)

Pomocí **zdroje zobrazení** z vašeho oblíbeného prohlížeče zkontrolujte generované značky. Část generovaného HTML je uvedena níže:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicky generované odkazy předat ID filmu s řetězcem dotazu `?id=1` `https://localhost:5001/Movies/Details?id=1`(například in).

### <a name="add-route-template"></a>Přidat šablonu postupu

Aktualizujte stránky upravit, podrobnosti a odstranit holicí strojek, abyste použili šablonu trasy {id:int}. Změňte direktivu stránky `@page` `@page "{id:int}"`pro každou z těchto stránek z na . Spusťte aplikaci a pak zobrazte zdroj. Vygenerovaný KÓD HTML přidá ID k části cesty adresy URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Požadavek na stránku se šablonou postupu {id:int}, která **neobsahuje** celé číslo, vrátí chybu HTTP 404 (nebyl nalezen). Například `http://localhost:5000/Movies/Details` vrátí chybu 404. Chcete-li, aby id `?` volitelné, připojit k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Chcete-li otestovat chování `@page "{id:int?}"`:

* Nastavte direktivu stránky v *pages/movies/details.cshtml* na `@page "{id:int?}"`.
* Nastavte bod přerušení `public async Task<IActionResult> OnGetAsync(int? id)` v (v *Pages/Movies/Details.cshtml.cs).*
* Přejděte na adresu `https://localhost:5001/Movies/Details/`.

Se `@page "{id:int}"` směrnicí není bod přerušení nikdy zasažen. Modul pro směrování vrátí protokol HTTP 404. Pomocí `@page "{id:int?}"`, `OnGetAsync` metoda `NotFound` vrátí (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Zkontrolovat zpracování výjimky souběžnosti

Zkontrolujte `OnPostAsync` metodu v souboru *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Předchozí kód zjistí výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny filmu.

Chcete-li `catch` blok otestovat:

* Nastavení zarážky`catch (DbUpdateConcurrencyException)`
* Vyberte **Upravit** film, proveďte změny, ale nezadávejte **uložit**.
* V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.
* V předchozím okně prohlížeče zaúčtujete změny filmu.

Produkční kód může chtít zjistit konflikty souběžnosti. Další informace naleznete [v tématu Zpracování konfliktů souběžnosti.](xref:data/ef-rp/concurrency)

### <a name="posting-and-binding-review"></a>Zaúčtování a závazná recenze

Prohlédněte si soubor *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Při požadavku HTTP GET na stránku Filmy/Úpravy `http://localhost:5000/Movies/Edit/2`(například ):

* Metoda `OnGetAsync` načte film z databáze a `Page` vrátí metodu.
* Metoda `Page` vykreslí *stránky /filmy/Edit.cshtml* Razor Page. Soubor *Pages/Movies/Edit.cshtml* obsahuje direktivu modelu (`@model RazorPagesMovie.Pages.Movies.EditModel`), která zpřístupňuje filmový model na stránce.
* Formulář Úpravy se zobrazí s hodnotami z filmu.

Když je stránka Filmy/Úpravy zaúčtována:

* Hodnoty formuláře na stránce jsou `Movie` vázány na vlastnost. Atribut `[BindProperty]` umožňuje [Model vazby](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Pokud jsou chyby ve stavu modelu `ReleaseDate` (například nelze převést na datum), formulář je znovu zobrazen s odeslanými hodnotami.
* Pokud nejsou k dispozici žádné chyby modelu, film se uloží.

Metody HTTP GET na stránkách Index, Create a Delete Razor postupujte podle podobného vzoru. Metoda HTTP `OnPostAsync` POST na stránce Vytvořit břitvu `OnPostAsync` se řídí podobným vzorem jako metoda na stránce Upravit holicí strojek.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: Práce s databází](xref:tutorials/razor-pages/sql)
> [Další: Přidat hledání](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Šálnatá filmová aplikace má dobrý začátek, ale prezentace není ideální. **Datum vydání** by mělo být **datum vydání** (dvě slova).

![Filmová aplikace otevřená v Chromu](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizace generovaného kódu

Otevřete soubor *Models/Movie.cs* a přidejte zvýrazněné řádky zobrazené v následujícím kódu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Anotace `[Column(TypeName = "decimal(18, 2)")]` dat umožňuje core entity `Price` framework správně mapovat na měnu v databázi. Další informace naleznete [v tématu Datové typy](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) je popsána v dalším kurzu. Display [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) Atribut určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

Přejděte na Stránky/filmy a najeďte na odkaz **Upravit a** podívejte se na cílovou adresu URL.

![Okno prohlížeče s myší na odkaz Upravit http://localhost:1234/Movies/Edit/5 a zobrazí se adresa URL odkazu](~/tutorials/razor-pages/da1/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značky ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Movies/Index.cshtml.*

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. V předchozím kódu `AnchorTagHelper` dynamicky generuje hodnotu `href` atributu HTML ze stránky Razor Page `asp-page`(trasa je`asp-route-id`relativní), a id trasy ( ). Další informace najdete [v tématu generování adres URL pro stránky.](xref:razor-pages/index#url-generation-for-pages)

Pomocí **zdroje zobrazení** z vašeho oblíbeného prohlížeče zkontrolujte generované značky. Část generovaného HTML je uvedena níže:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicky generované odkazy předat ID filmu s řetězcem dotazu `?id=1` `https://localhost:5001/Movies/Details?id=1`(například in).

Aktualizujte stránky upravit, podrobnosti a odstranit holicí strojek, abyste použili šablonu trasy {id:int}. Změňte direktivu stránky `@page` `@page "{id:int}"`pro každou z těchto stránek z na . Spusťte aplikaci a pak zobrazte zdroj. Vygenerovaný KÓD HTML přidá ID k části cesty adresy URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Požadavek na stránku se šablonou postupu {id:int}, která **neobsahuje** celé číslo, vrátí chybu HTTP 404 (nebyl nalezen). Například `http://localhost:5000/Movies/Details` vrátí chybu 404. Chcete-li, aby id `?` volitelné, připojit k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Chcete-li otestovat chování `@page "{id:int?}"`:

* Nastavte direktivu stránky v *pages/movies/details.cshtml* na `@page "{id:int?}"`.
* Nastavte bod přerušení `public async Task<IActionResult> OnGetAsync(int? id)` v (v *Pages/Movies/Details.cshtml.cs).*
* Přejděte na adresu `https://localhost:5001/Movies/Details/`.

Se `@page "{id:int}"` směrnicí není bod přerušení nikdy zasažen. Modul pro směrování vrátí protokol HTTP 404. Pomocí `@page "{id:int?}"`, `OnGetAsync` metoda `NotFound` vrátí (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Zkontrolovat zpracování výjimky souběžnosti

Zkontrolujte `OnPostAsync` metodu v souboru *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Předchozí kód zjistí výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny filmu.

Chcete-li `catch` blok otestovat:

* Nastavení zarážky`catch (DbUpdateConcurrencyException)`
* Vyberte **Upravit** film, proveďte změny, ale nezadávejte **uložit**.
* V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.
* V předchozím okně prohlížeče zaúčtujete změny filmu.

Produkční kód může chtít zjistit konflikty souběžnosti. Další informace naleznete [v tématu Zpracování konfliktů souběžnosti.](xref:data/ef-rp/concurrency)

### <a name="posting-and-binding-review"></a>Zaúčtování a závazná recenze

Prohlédněte si soubor *Pages/Movies/Edit.cshtml.cs:*

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Při požadavku HTTP GET na stránku Filmy/Úpravy `http://localhost:5000/Movies/Edit/2`(například ):

* Metoda `OnGetAsync` načte film z databáze a `Page` vrátí metodu. 
* Metoda `Page` vykreslí *stránky /filmy/Edit.cshtml* Razor Page. Soubor *Pages/Movies/Edit.cshtml* obsahuje direktivu modelu (`@model RazorPagesMovie.Pages.Movies.EditModel`), která zpřístupňuje filmový model na stránce.
* Formulář Úpravy se zobrazí s hodnotami z filmu.

Když je stránka Filmy/Úpravy zaúčtována:

* Hodnoty formuláře na stránce jsou `Movie` vázány na vlastnost. Atribut `[BindProperty]` umožňuje [Model vazby](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Pokud jsou chyby ve stavu modelu `ReleaseDate` (například nelze převést na datum), formulář se zobrazí s odeslanými hodnotami.
* Pokud nejsou k dispozici žádné chyby modelu, film se uloží.

Metody HTTP GET na stránkách Index, Create a Delete Razor postupujte podle podobného vzoru. Metoda HTTP `OnPostAsync` POST na stránce Vytvořit břitvu `OnPostAsync` se řídí podobným vzorem jako metoda na stránce Upravit holicí strojek.

Hledání je přidánv dalším kurzu.

## <a name="additional-resources"></a>Další zdroje

* [Verze tohoto kurzu pro YouTube](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Předchozí: Práce s databází](xref:tutorials/razor-pages/sql)
> [Další: Přidat hledání](xref:tutorials/razor-pages/search)

::: moniker-end
