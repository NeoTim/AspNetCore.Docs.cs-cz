---
title: Metody a zobrazení řadiče v ASP.NET Core
author: rick-anderson
description: Naučte se pracovat s metodami, zobrazeními a daty v ASP.NET Core.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 87b3cb2f4429157123d30274d1f12cd589c1cc99
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80242507"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a>Metody a zobrazení řadiče v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Máme dobrý start do aplikace filmu, ale prezentace není ideální, například **ReleaseDate** by měla být dvě slova.

![Zobrazení indexu: Datum vydání je jedno slovo (bez místa) a každé datum vydání filmu zobrazuje čas 12:00](working-with-sql/_static/m55.png)

Otevřete soubor *Models/Movie.cs* a přidejte zvýrazněné řádky zobrazené níže:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Pokrýváme [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) v dalším kurzu. Display [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) Atribut určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

Anotace `[Column(TypeName = "decimal(18, 2)")]` dat je vyžadována, aby `Price` bylo jádro entity správně mapováno na měnu v databázi. Další informace naleznete [v tématu Datové typy](/ef/core/modeling/relational/data-types).

Přejděte `Movies` na ovladač a podržte ukazatel myši nad odkazem **Pro úpravy,** abyste viděli cílovou adresu URL.

![Okno prohlížeče s myší na odkaz Upravit https://localhost:5001/Movies/Edit/5 a zobrazí se adresa URL odkazu](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou generovány pomocí základního pomocníka mvc kotevní značky v souboru *Zobrazení/Filmy/Index.cshtml.*

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. Ve výše uvedeném `AnchorTagHelper` kódu dynamicky `href` generuje hodnotu atributu HTML z metody akce kontroleru a id trasy. Zdroj **zobrazení** můžete použít z oblíbeného prohlížeče nebo pomocí vývojářských nástrojů prozkoumat generované značky. Část generovaného HTML je uvedena níže:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Odvolání formátu [pro sadu směrování](xref:mvc/controllers/routing) v *souboru Startup.cs:*

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core `https://localhost:5001/Movies/Edit/4` se překládá `Edit` do požadavku `Movies` na metodu `Id` akce řadiče s parametrem 4. (Metody kontroleru jsou také známé jako metody akce.)

[Tag Helpers](xref:mvc/views/tag-helpers/intro) jsou jednou z nejpopulárnějších nových funkcí v ASP.NET Core. Další informace naleznete v tématu [Další zdroje .](#additional-resources)

<a name="get-post"></a>

Otevřete `Movies` řadič a `Edit` zkontrolujte dvě metody akce. Následující kód ukazuje `HTTP GET Edit` metodu, která načte film a naplní editační formulář generovaný souborem *Edit.cshtml* Razor.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Následující kód ukazuje `HTTP POST Edit` metodu, která zpracovává zaúčtované hodnoty filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Následující kód ukazuje `HTTP POST Edit` metodu, která zpracovává zaúčtované hodnoty filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Atribut `[Bind]` je jedním ze způsobů ochrany proti [nadměrnému zaúčtování](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Do atributu, `[Bind]` který chcete změnit, byste měli zahrnout pouze vlastnosti. Další informace naleznete v [tématu Ochrana řadiče před přeúčtováním](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) poskytují alternativní přístup k zabránění nadměrnému zaúčtování.

Všimněte `Edit` si, že druhé `[HttpPost]` metodě akce předchází atribut.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

Atribut `HttpPost` určuje, že `Edit` tuto metodu lze `POST` vyvolat *pouze* pro požadavky. Atribut můžete `[HttpGet]` použít na první metodu úprav, ale `[HttpGet]` to není nutné, protože je výchozí.

Atribut `ValidateAntiForgeryToken` se používá k [zabránění padělání požadavku](xref:security/anti-request-forgery) a je spárován s tokenem proti padělání generovaným v souboru zobrazení úprav *(Zobrazení/Filmy/Edit.cshtml).* Soubor zobrazení úprav generuje token anti-padělek pomocí [pomocníka pro označení formuláře](xref:mvc/views/working-with-forms).

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

Pomocník [s značkami formuláře](xref:mvc/views/working-with-forms) generuje skrytý token proti padělání, `[ValidateAntiForgeryToken]` který musí odpovídat generovanému `Edit` tokenu proti padělání v metodě kontroleru filmy. Další informace naleznete [v tématu Anti-Request Pagery](xref:security/anti-request-forgery).

Metoda `HttpGet Edit` převezme parametr `ID` movie, vyhledá film pomocí `FindAsync` metody Entity Framework a vrátí vybraný film do zobrazení Úpravy. Pokud film nelze najít, `NotFound` (HTTP 404) je vrácena.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Když systém vytváření uživatelského rozhraní vytvořil zobrazení úprav, prozkoumal `Movie` třídu `<label>` `<input>` a vytvořil kód k vykreslení a prvky pro každou vlastnost třídy. Následující příklad ukazuje zobrazení úprav, které bylo generováno systémem generování uživatelského zařízení sady Visual Studio:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Všimněte si, jak `@model MvcMovie.Models.Movie` má šablona zobrazení příkaz v horní části souboru. `@model MvcMovie.Models.Movie`určuje, že pohled očekává, že model pro šablonu zobrazení bude typu `Movie`.

Kód sklopný generováním hesla používá několik metod tag helper umocnit značky HTML. Pomocník [značky - štítek](xref:mvc/views/working-with-forms) zobrazí název pole ("Název", "Datum vydání", "Žánr" nebo "Cena"). Pomocník [vstupních značek](xref:mvc/views/working-with-forms) vykreslí element HTML. `<input>` Pomocné [s ověřovacíznačkou zobrazí](xref:mvc/views/working-with-forms) všechny ověřovací zprávy přidružené k této vlastnosti.

Spusťte aplikaci `/Movies` a přejděte na adresu URL. Klikněte na odkaz **Upravit.** V prohlížeči zobrazte zdroj stránky. Vygenerovaný `<form>` kód HTML pro prvek je uveden níže.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Prvky `<input>` jsou `HTML <form>` v `action` prvku, jehož atribut `/Movies/Edit/id` je nastaven na příspěvek na adresu URL. Data formuláře budou po klepnutí `Save` na tlačítko odeslána na server. Poslední řádek před `</form>` uzavíracím prvkem zobrazuje skrytý token [XSRF](xref:security/anti-request-forgery) generovaný [pomocníkem značky formuláře](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Zpracování požadavku POST

Následující výpis ukazuje `[HttpPost]` verzi `Edit` metody akce.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

Atribut `[ValidateAntiForgeryToken]` ověří skrytý token [XSRF](xref:security/anti-request-forgery) generovaný generátorem tokenů proti padělání v [pomocníku značky formuláře.](xref:mvc/views/working-with-forms)

Systém [vazby modelu](xref:mvc/models/model-binding) přebírá zaúčtované hodnoty formuláře a vytvoří `Movie` objekt, který je předán jako `movie` parametr. Metoda `ModelState.IsValid` ověří, zda data odeslaná ve formuláři lze upravit (upravit nebo aktualizovat) `Movie` objekt. Pokud jsou data platná, jsou uložena. Aktualizovaná (upravená) filmová data jsou `SaveChangesAsync` uložena do databáze voláním metody kontextu databáze. Po uložení dat kód přesměruje uživatele `Index` na metodu `MoviesController` akce třídy, která zobrazuje kolekci filmů, včetně právě provedených změn.

Před odesláním formuláře na server ověření na straně klienta zkontroluje všechna ověřovací pravidla pro pole. Pokud se zobrazí chyby ověření, zobrazí se chybová zpráva a formulář nebude zaúčtován. Pokud je JavaScript zakázán, nebudete mít ověření na straně klienta, ale server zjistí zaúčtované hodnoty, které nejsou platné, a hodnoty formuláře budou znovu zobrazeny s chybovými zprávami. Později v kurzu prozkoumáme [ověření modelu](xref:mvc/models/validation) podrobněji. Pomocník [pro ověřovací značky](xref:mvc/views/working-with-forms) v šabloně *zobrazení/filmy/upravit.cshtml* se postará o zobrazení příslušných chybových zpráv.

![Upravit zobrazení: Výjimka pro nesprávnou hodnotu Cena abc uvádí, že pole Cena musí být číslo. Výjimka pro nesprávnou hodnotu data vydání stavů xyz Zadejte platné datum.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Všechny `HttpGet` metody v řadiči filmu postupujte podle podobného vzoru. Dostanou objekt filmu (nebo seznam objektů, v `Index`případě ) a předat objekt (model) do pohledu. Metoda `Create` předá `Create` do zobrazení prázdný objekt filmu. Všechny metody, které vytvářejí, upravují, odstraňují `[HttpPost]` nebo jinak upravují data, tak činí v přetížení metody. Úprava dat `HTTP GET` v metodě je bezpečnostní riziko. Úprava dat `HTTP GET` v metodě také porušuje osvědčené postupy protokolu HTTP a architektonický vzor [REST,](http://rest.elkstein.org/) který určuje, že požadavky GET by neměly měnit stav vaší aplikace. Jinými slovy, provedení operace GET by měla být bezpečná operace, která nemá žádné vedlejší účinky a nemění trvalá data.

## <a name="additional-resources"></a>Další zdroje

* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod do pomocné spoje značek](xref:mvc/views/tag-helpers/intro)
* [Pomocné s tagy autora](xref:mvc/views/tag-helpers/authoring)
* [Proti žádosti padělání](xref:security/anti-request-forgery)
* Chraňte svůj ovladač před [přeúčtováním](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [Zobrazit modely](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Pomocná rutina značky formuláře](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky popisku](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky výběru](xref:mvc/views/working-with-forms)
* [Pomocné s ověřovacíznačkou](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Předchozí](working-with-sql.md)
> [další](search.md)  
