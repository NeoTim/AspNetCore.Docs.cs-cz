---
title: 5. část, jak aktualizovat vygenerované stránky v aplikaci ASP.NET Core
author: rick-anderson
description: 5. část série kurzů na Razor stránkách.
ms.author: riande
ms.date: 12/20/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 144c4e7f07986c0a10089d74991fe3b44c013894
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652859"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a>5. část, jak aktualizovat vygenerované stránky v aplikaci ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální. **ReleaseDate** by mělo být **Datum vydání** (dvě slova).

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizovat generovaný kód

Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]`Datová anotace umožňuje, aby Entity Framework Core správně namapovala `Price` na měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

V dalším kurzu jsou uvedena tato [Anotace](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) . Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL odkazu. http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Movies/index. cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor V předchozím kódu `AnchorTagHelper` dynamicky generuje `href` hodnotu atributu HTML ze Razor stránky (trasa je relativní), `asp-page` a ID trasy ( `asp-route-id` ). Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .

Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód. Část vygenerovaného kódu HTML je zobrazena níže:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1` ).

### <a name="add-route-template"></a>Přidat šablonu směrování

Aktualizujte stránky upravit, podrobnosti a odstranit Razor tak, aby používaly šablonu trasy {ID: int}. Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"` . Spusťte aplikaci a potom zobrazte zdroj. Vygenerovaný kód HTML přidá ID do části cesty adresy URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno). Například `http://localhost:5000/Movies/Details` vrátí chybu 404. Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Postup při testování chování `@page "{id:int?}"` :

* Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"` .
* Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).
* Přejděte na adresu `https://localhost:5001/Movies/Details/`.

V `@page "{id:int}"` případě direktivy není bod přerušení nikdy vyzasažen. Modul směrování vrátí HTTP 404. Pomocí nástroje `@page "{id:int?}"` `OnGetAsync` vrátí metoda `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Kontrola zpracování výjimek souběžnosti

Zkontrolujte `OnPostAsync` metodu v souboru *Pages/video/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.

Test `catch` bloku:

* Nastavit zarážku na`catch (DbUpdateConcurrencyException)`
* Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.
* V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.
* V předchozím okně prohlížeče se změny publikují ve videu.

Produkční kód může chtít detekovat konflikty souběžnosti. Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Publikování a kontrola vazeb

Prověřte soubory *Pages/video/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2` ):

* `OnGetAsync`Metoda načte film z databáze a vrátí `Page` metodu.
* `Page`Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor . Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu ( `@model RazorPagesMovie.Pages.Movies.EditModel` ), která umožňuje, aby byl model filmu dostupný na stránce.
* Formulář pro úpravy se zobrazí s hodnotami z filmu.

Když se publikuje video/úprava stránky:

* Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost. `[BindProperty]`Atribut umožňuje [vazbu modelu](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), formulář se znovu zobrazí u odeslaných hodnot.
* Pokud nedochází k žádným chybám modelu, je film uložený.

Metody HTTP GET v indexech, vytváření a odstraňování Razor stránek následují podobně jako vzor. Metoda HTTP POST na `OnPostAsync` Razor stránce vytvořit následuje podobný vzor jako metoda na `OnPostAsync` stránce pro úpravy Razor .

## <a name="additional-resources"></a>Další zdroje

> [!div class="step-by-step"]
> [Předchozí: práce s databází](xref:tutorials/razor-pages/sql) 
>  [Další: Přidat hledání](xref:tutorials/razor-pages/search)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikace filmového vygenerovaného uživatelského rozhraní má dobrý Start, ale prezentace není ideální. **ReleaseDate** by mělo být **Datum vydání** (dvě slova).

![Aplikace Movie otevřená v Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizovat generovaný kód

Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky, které jsou uvedeny v následujícím kódu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

`[Column(TypeName = "decimal(18, 2)")]`Datová anotace umožňuje, aby Entity Framework Core správně namapovala `Price` na měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

V dalším kurzu jsou uvedena tato [Anotace](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) . Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

Přejděte na stránky a filmy a najeďte myší na odkaz pro **Úpravy** a zobrazte tak cílovou adresu URL.

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL odkazu. http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v souboru *Pages/Movies/index. cshtml* .

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor V předchozím kódu `AnchorTagHelper` dynamicky generuje `href` hodnotu atributu HTML ze Razor stránky (trasa je relativní), `asp-page` a ID trasy ( `asp-route-id` ). Další informace najdete v tématu [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) .

Pomocí **zobrazení zdroje** z oblíbeného prohlížeče Prohlédněte vygenerovaný kód. Část vygenerovaného kódu HTML je zobrazena níže:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicky vygenerované odkazy předají ID filmu pomocí řetězce dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1` ).

Aktualizujte stránky upravit, podrobnosti a odstranit Razor tak, aby používaly šablonu trasy {ID: int}. Změňte direktivu Page na každou z těchto stránek z `@page` na `@page "{id:int}"` . Spusťte aplikaci a potom zobrazte zdroj. Vygenerovaný kód HTML přidá ID do části cesty adresy URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Požadavek na stránku se šablonou trasy {ID: int}, která **nezahrnuje celé** číslo, vrátí chybu HTTP 404 (Nenalezeno). Například `http://localhost:5000/Movies/Details` vrátí chybu 404. Pokud chcete ID nastavit jako volitelné, připojovat `?` se k omezení trasy:

 ```cshtml
@page "{id:int?}"
```

Postup při testování chování `@page "{id:int?}"` :

* Nastavte direktivu Page na *Pages/video/details. cshtml* na `@page "{id:int?}"` .
* Nastavte bod přerušení v `public async Task<IActionResult> OnGetAsync(int? id)` ( *stránky/filmy/podrobnosti. cshtml. cs*).
* Přejděte na adresu `https://localhost:5001/Movies/Details/`.

V `@page "{id:int}"` případě direktivy není bod přerušení nikdy vyzasažen. Modul směrování vrátí HTTP 404. Pomocí nástroje `@page "{id:int?}"` `OnGetAsync` vrátí metoda `NotFound` (HTTP 404).

### <a name="review-concurrency-exception-handling"></a>Kontrola zpracování výjimek souběžnosti

Zkontrolujte `OnPostAsync` metodu v souboru *Pages/video/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Předchozí kód detekuje výjimky souběžnosti, když jeden klient odstraní film a druhý klient odešle změny do filmu.

Test `catch` bloku:

* Nastavit zarážku na`catch (DbUpdateConcurrencyException)`
* Vyberte **Upravit** pro film, proveďte změny, ale nezadávejte možnost **Uložit**.
* V jiném okně prohlížeče vyberte odkaz **Odstranit** pro stejný film a pak film odstraňte.
* V předchozím okně prohlížeče se změny publikují ve videu.

Produkční kód může chtít detekovat konflikty souběžnosti. Další informace najdete v tématu [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) .

### <a name="posting-and-binding-review"></a>Publikování a kontrola vazeb

Prověřte soubory *Pages/video/Edit. cshtml. cs* :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Když se na stránce filmy/úpravy provede požadavek HTTP GET (například `http://localhost:5000/Movies/Edit/2` ):

* `OnGetAsync`Metoda načte film z databáze a vrátí `Page` metodu. 
* `Page`Metoda vykresluje stránku *stránky/filmy/upravit. cshtml* Razor . Soubor *Pages/video/Edit. cshtml* obsahuje direktivu modelu ( `@model RazorPagesMovie.Pages.Movies.EditModel` ), která umožňuje, aby byl model filmu dostupný na stránce.
* Formulář pro úpravy se zobrazí s hodnotami z filmu.

Když se publikuje video/úprava stránky:

* Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost. `[BindProperty]`Atribut umožňuje [vazbu modelu](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Pokud dojde k chybám ve stavu modelu (například `ReleaseDate` nelze je převést na datum), zobrazí se formulář s odeslanými hodnotami.
* Pokud nedochází k žádným chybám modelu, je film uložený.

Metody HTTP GET v indexech, vytváření a odstraňování Razor stránek následují podobně jako vzor. Metoda HTTP POST na `OnPostAsync` Razor stránce vytvořit následuje podobný vzor jako metoda na `OnPostAsync` stránce pro úpravy Razor .

Hledání se přidá v dalším kurzu.

## <a name="additional-resources"></a>Další zdroje

* [Verze YouTube tohoto kurzu](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Předchozí: práce s databází](xref:tutorials/razor-pages/sql) 
>  [Další: Přidat hledání](xref:tutorials/razor-pages/search)

::: moniker-end
