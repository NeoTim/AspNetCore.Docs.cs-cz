
`[Column(TypeName = "decimal(18, 2)")]` Anotace dat se vyžaduje, aby správně můžete mapovat Entity Framework Core `Price` měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

Dokončené modelu:

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1)]

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

Dynamicky generovaná odkazy předají ID filmů s řetězcem dotazu (například `http://localhost:5000/Movies/Details?id=2`).

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

::: moniker range="= aspnetcore-2.0"

### <a name="update-concurrency-exception-handling"></a>Aktualizovat zpracování výjimky souběžnosti

Aktualizace `OnPostAsync` metodu *Pages/Movies/Edit.cshtml.cs* souboru. Následující zvýrazněný kód ukazuje změny:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit.cshtml.cs?name=snippet1&highlight=16-23)]

Předchozí kód výjimky souběžnosti rozpozná pouze, pokud první souběžných odstraní videa a druhý souběžných odešle změny videa.

K testování `catch` blok:

* Nastavení zarážky v `catch (DbUpdateConcurrencyException)`
* Úprava videa.
* V jiném okně prohlížeče, vyberte **odstranit** propojit pro stejný film a pak odstraňte video.
* V předchozím okně prohlížeče se publikovat změny videa.

Produkční kód by obvykle zjišťování konfliktů souběžnosti, pokud dvě nebo víc klientů současně aktualizuje záznam. Zobrazit [zpracování konfliktů souběžnosti](xref:data/ef-rp/concurrency) Další informace.

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

* Pokud jsou ve stavu modelu chyby (například `ReleaseDate` nelze převést na datum), znovu odeslání formuláře pomocí zadané hodnoty.
* Pokud nejsou žádné chyby modelu, video se uloží.

Metody GET protokolu HTTP v indexu, vytvořit a odstranit Razor pages podle podobný vzorec. HTTP POST `OnPostAsync` metody na stránce vytvořit Razor následuje podobný vzorec k `OnPostAsync` metody ve stránce Upravit Razor.

V dalším kurzu se přidá vyhledávání.
