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
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>Aktualizovat vygenerované stránky v aplikaci ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální. **ReleaseDate** by mělo být **Datum vydání** (dvě slova).

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizovat generovaný kód

Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Datová anotace umožňuje, aby Entity Framework Core správně `Price` namapovala na měnu v databázi. `[Column(TypeName = "decimal(18, 2)")]` Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

[](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) V dalším kurzu jsou uvedena tato anotace. Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL http://localhost:1234/Movies/Edit/5 odkazu.](~/tutorials/razor-pages/da1/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru Pages */Movies/index. cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. V předchozím kódu `AnchorTagHelper` dynamicky generuje hodnotu atributu HTML `href` ze stránky Razor (trasa `asp-page`je relativní), a ID trasy (`asp-route-id`). Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .

Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód. Část vygenerovaného kódu HTML je zobrazena níže:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1`).

Aktualizujte Razor Pages upravit, podrobnosti a odstranit, aby se použila šablona trasy {ID: int}. Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"`. Spusťte aplikaci a potom zobrazte zdroj. Vygenerovaný kód HTML přidá ID do části cesty adresy URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno). Například `http://localhost:5000/Movies/Details` vrátí chybu 404. Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Postup při testování chování `@page "{id:int?}"`:

* Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"`.
* Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).
* Přejděte na adresu `https://localhost:5001/Movies/Details/`.

V případě `@page "{id:int}"` direktivy není bod přerušení nikdy vyzasažen. Modul směrování vrátí HTTP 404. Pomocí nástroje `@page "{id:int?}"`vrátí `NotFound` metoda (HTTP 404). `OnGetAsync`

### <a name="review-concurrency-exception-handling"></a>Kontrola zpracování výjimek souběžnosti

Zkontrolujte metodu v souboru *Pages/video/Edit. cshtml. cs:* `OnPostAsync`

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.

Test `catch` bloku:

* Nastavit zarážku na`catch (DbUpdateConcurrencyException)`
* Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.
* V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.
* V předchozím okně prohlížeče se změny publikují ve videu.

Produkční kód může chtít detekovat konflikty souběžnosti. Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Publikování a kontrola vazeb

Prověřte soubory Pages/ *video/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2`):

* Metoda načte film z databáze a `Page` vrátí metodu. `OnGetAsync`
* Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor. `Page` Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu (`@model RazorPagesMovie.Pages.Movies.EditModel`), která umožňuje, aby byl model filmu dostupný na stránce.
* Formulář pro úpravy se zobrazí s hodnotami z filmu.

Když se publikuje video/úprava stránky:

* Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost. Atribut umožňuje [vazbu modelu.](xref:mvc/models/model-binding) `[BindProperty]`

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), formulář se znovu zobrazí u odeslaných hodnot.
* Pokud nedochází k žádným chybám modelu, je film uložený.

Metody HTTP GET v indexech, vytváření a odstraňování stránek Razor následují podobně jako vzor. Metoda HTTP Post `OnPostAsync` na stránce vytvořit Razor následuje podobný vzor `OnPostAsync` jako metoda na stránce Upravit Razor.

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí Práce s databází](xref:tutorials/razor-pages/sql)
> Next[: Přidat hledání](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální. **ReleaseDate** by mělo být **Datum vydání** (dvě slova).

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizovat generovaný kód

Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

Datová anotace umožňuje, aby Entity Framework Core správně `Price` namapovala na měnu v databázi. `[Column(TypeName = "decimal(18, 2)")]` Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

[](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) V dalším kurzu jsou uvedena tato anotace. Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL http://localhost:1234/Movies/Edit/5 odkazu.](~/tutorials/razor-pages/da1/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru Pages */Movies/index. cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. V předchozím kódu `AnchorTagHelper` dynamicky generuje hodnotu atributu HTML `href` ze stránky Razor (trasa `asp-page`je relativní), a ID trasy (`asp-route-id`). Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .

Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód. Část vygenerovaného kódu HTML je zobrazena níže:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1`).

Aktualizujte Razor Pages upravit, podrobnosti a odstranit, aby se použila šablona trasy {ID: int}. Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"`. Spusťte aplikaci a potom zobrazte zdroj. Vygenerovaný kód HTML přidá ID do části cesty adresy URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno). Například `http://localhost:5000/Movies/Details` vrátí chybu 404. Pokud chcete ID nastavit jako volitelné, `?` připojovat se k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Postup při testování chování `@page "{id:int?}"`:

* Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"`.
* Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).
* Přejděte na adresu `https://localhost:5001/Movies/Details/`.

V případě `@page "{id:int}"` direktivy není bod přerušení nikdy vyzasažen. Modul směrování vrátí HTTP 404. Pomocí nástroje `@page "{id:int?}"`vrátí `NotFound` metoda (HTTP 404). `OnGetAsync`

### <a name="review-concurrency-exception-handling"></a>Kontrola zpracování výjimek souběžnosti

Zkontrolujte metodu v souboru *Pages/video/Edit. cshtml. cs:* `OnPostAsync`

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.

Test `catch` bloku:

* Nastavit zarážku na`catch (DbUpdateConcurrencyException)`
* Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.
* V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.
* V předchozím okně prohlížeče se změny publikují ve videu.

Produkční kód může chtít detekovat konflikty souběžnosti. Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Publikování a kontrola vazeb

Prověřte soubory Pages/ *video/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2`):

* Metoda načte film z databáze a `Page` vrátí metodu. `OnGetAsync` 
* Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor. `Page` Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu (`@model RazorPagesMovie.Pages.Movies.EditModel`), která umožňuje, aby byl model filmu dostupný na stránce.
* Formulář pro úpravy se zobrazí s hodnotami z filmu.

Když se publikuje video/úprava stránky:

* Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost. Atribut umožňuje [vazbu modelu.](xref:mvc/models/model-binding) `[BindProperty]`

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), zobrazí se formulář s odeslanými hodnotami.
* Pokud nedochází k žádným chybám modelu, je film uložený.

Metody HTTP GET v indexech, vytváření a odstraňování stránek Razor následují podobně jako vzor. Metoda HTTP Post `OnPostAsync` na stránce vytvořit Razor následuje podobný vzor `OnPostAsync` jako metoda na stránce Upravit Razor.

Hledání se přidá v dalším kurzu.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Předchozí Práce s databází](xref:tutorials/razor-pages/sql)
> Next[: Přidat hledání](xref:tutorials/razor-pages/search)

::: moniker-end
