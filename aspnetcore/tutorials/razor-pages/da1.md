---
title: Aktualizovat generované stránky v aplikaci ASP.NET Core
author: rick-anderson
description: Zjistěte, jak aktualizovat generované stránky v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 19f54e04e748f5cf0469c2b5428800694d2c9344
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899190"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a>Aktualizovat generované stránky v aplikaci ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Vygenerované filmová aplikace má dobrý začátek, ale v prezentaci není ideální. **Datum releaseDate** by měl být **datum vydání** (dvě slova).

![Otevřít v prohlížeči Chrome aplikace Movie](sql/_static/m55.png)

## <a name="update-the-generated-code"></a>Aktualizace generovaného kódu

Otevřít *Models/Movie.cs* a přidejte zvýrazněné řádky je znázorněno v následujícím kódu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=12,17)]

`[Column(TypeName = "decimal(18, 2)")]` Umožňuje anotaci dat Entity Framework Core správně mapovat `Price` měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) se věnujeme v dalším kurzu. [Zobrazit](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) atribut určuje, co má být zobrazen pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). [Datový typ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) atribut určuje typ dat (datum), takže se nezobrazí čas informací uložených v poli.

Přejděte na stránky/filmy a najeďte myší **upravit** odkaz zobrazíte cílové adrese URL.

![Okno prohlížeče pomocí myši nad odkaz pro úpravy a odkazem na adresu Url http://localhost:1234/Movies/Edit/5 se zobrazí](~/tutorials/razor-pages/da1/edit7.png)

**Upravit**, **podrobnosti**, a **odstranit** vygeneroval odkazy [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) v *stránek/filmy / Index.cshtml* souboru.

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. V předchozím kódu `AnchorTagHelper` dynamicky generuje kód HTML `href` hodnotu atributu ze stránky Razor (trasy je relativní), `asp-page`a id tras (`asp-route-id`). Zobrazit [generování adresy URL pro stránky](xref:razor-pages/index#url-generation-for-pages) Další informace.

Použití **zobrazit zdroj** z svůj oblíbený prohlížeč prozkoumat generovaného kódu. Část generovaný kód HTML je zobrazena níže:

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

Dynamicky generovaná odkazy předají ID filmů s řetězcem dotazu (například `?id=1` v `https://localhost:5001/Movies/Details?id=1`).

Aktualizujte upravit, podrobnosti a odstranit Razor Pages použít šablonu trasy "{id: int}". Změnit direktivě stránky pro každou z těchto stránek z `@page` k `@page "{id:int}"`. Spusťte aplikaci a pak zobrazte zdroj. Generovaný kód jazyka HTML přidá ID část cesty adresy URL:

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

Požadavek na stránku se šablona trasy "{id: int}", která provádí **není** zahrnují celé číslo, vrátí chybu HTTP 404 (Nenalezeno). Například `http://localhost:5000/Movies/Details` vrátí chybu 404. Chcete-li nastavit ID volitelný, přidejte `?` pro dané omezení trasy:

 ```cshtml
@page "{id:int?}"
```

K otestování chování `@page "{id:int?}"`:

* Nastavit direktivě stránky *Pages/Movies/Details.cshtml* k `@page "{id:int?}"`.
* Nastavte zarážky `public async Task<IActionResult> OnGetAsync(int? id)` (v *Pages/Movies/Details.cshtml.cs*).
* Přejděte na adresu `https://localhost:5001/Movies/Details/`.

S `@page "{id:int}"` direktiv, bodu přerušení je nikdy dosaženo. Modul směrování vrátí chyby HTTP 404. Pomocí `@page "{id:int?}"`, `OnGetAsync` vrátí metoda `NotFound` (HTTP 404).

Však není doporučena, můžete napsat `OnGetAsync` – metoda (v *Pages/Movies/Delete.cshtml.cs*) jako:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Delete.cshtml.cs?name=snippet)]

Předchozí kód testu:

* Vyberte **odstranit** odkaz.
* Odeberte ID z adresy URL. Například změnit `https://localhost:5001/Movies/Delete/8` k `https://localhost:5001/Movies/Delete`.
* Krokovat kód v ladicím programu.

### <a name="review-concurrency-exception-handling"></a>Kontrola zpracování výjimky souběžnosti

Zkontrolujte `OnPostAsync` metoda ve *Pages/Movies/Edit.cshtml.cs* souboru:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

Předchozí kód rozpozná výjimky souběžnosti, pokud jeden klient odstraní videa a další klient odešle změny videa.

K testování `catch` blok:

* Nastavení zarážky v `catch (DbUpdateConcurrencyException)`
* Vyberte **upravit** pro videa, provést změny, ale nezadávejte **Uložit**.
* V jiném okně prohlížeče, vyberte **odstranit** propojit pro stejný film a pak odstraňte video.
* V předchozím okně prohlížeče se publikovat změny videa.

Produkční kód může být vhodné k detekci konfliktů souběžnosti. Zobrazit [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) Další informace.

### <a name="posting-and-binding-review"></a>Účtování a vazby revize

Zkontrolujte *Pages/Movies/Edit.cshtml.cs* souboru:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

Pokud je požadavek HTTP GET provedené na stránce videa nebo upravit (například `http://localhost:5000/Movies/Edit/2`):

* `OnGetAsync` Metoda načítá videa z databáze a vrátí `Page` metody. 
* `Page` Metoda vykreslí *Pages/Movies/Edit.cshtml* stránky Razor. *Pages/Movies/Edit.cshtml* soubor obsahuje model – direktiva (`@model RazorPagesMovie.Pages.Movies.EditModel`), které zpřístupňuje model video na stránce.
* Zobrazí se formulář pro úpravy s hodnotami z videa.

Při odeslání videa a upravovat stránky:

* Hodnoty formuláře na stránce jsou vázány na `Movie` vlastnost. `[BindProperty]` Atribut umožňuje [vazby modelu](xref:mvc/models/model-binding).

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* Pokud jsou ve stavu modelu chyby (například `ReleaseDate` nelze převést na datum), pomocí zadané hodnoty se zobrazí formulář.
* Pokud nejsou žádné chyby modelu, video se uloží.

Metody GET protokolu HTTP v indexu, vytvořit a odstranit Razor pages podle podobný vzorec. HTTP POST `OnPostAsync` metody na stránce vytvořit Razor následuje podobný vzorec k `OnPostAsync` metody ve stránce Upravit Razor.

V dalším kurzu se přidá vyhledávání.

## <a name="additional-resources"></a>Další zdroje

* [Verzi tohoto kurzu na webu YouTube](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> [Předchozí: Práce s databází](xref:tutorials/razor-pages/sql)
> [Další: Přidat vyhledávání](xref:tutorials/razor-pages/search)
