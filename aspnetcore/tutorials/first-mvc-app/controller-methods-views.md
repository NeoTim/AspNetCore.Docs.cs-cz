---
title: Část 6, metody kontroleru a zobrazení v ASP.NET Core
author: rick-anderson
description: Část 6 – přidání modelu do aplikace ASP.NET Core MVC
ms.author: riande
ms.date: 12/13/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: a22bc9f9156b671d7bf17b56b62e384a60b2568b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405260"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a>Část 6, metody kontroleru a zobrazení v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Je dobré začít s aplikací pro video, ale prezentace není ideální, například **ReleaseDate** by měla být dvě slova.

![Zobrazení indexu: Datum vydání je jedno slovo (bez mezer) a každé datum vydání videa zobrazuje čas 12](working-with-sql/_static/m55.png)

Otevřete soubor *Models/video. cs* a přidejte zvýrazněné řádky zobrazené níže:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

V dalším kurzu se zaměříme na tato [Dataanotace](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) . Atribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) určuje, co se má zobrazit pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum), takže se nezobrazí informace o čase uložené v poli.

`[Column(TypeName = "decimal(18, 2)")]`Datová anotace je vyžadována, aby Entity Framework Core mohl správně mapovat `Price` na měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

Přejděte k `Movies` řadiči a podržením ukazatele myši na odkaz pro **Úpravy** Zobrazte cílovou adresu URL.

![Zobrazí se okno prohlížeče s myší přes odkaz upravit a adresa URL odkazu. https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Odkazy **Upravit**, **Podrobnosti**a **Odstranit** jsou vygenerovány pomocníkem základní značky kotvící tag MVC v souboru *views/Movies/index. cshtml* .

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Pomáhat pomocníkům](xref:mvc/views/tag-helpers/intro) při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor Ve výše uvedeném kódu `AnchorTagHelper` dynamicky generuje `href` hodnotu atributu HTML z metody akce kontroleru a ID trasy. Pomocí **zobrazení zdroje** z oblíbeného prohlížeče nebo pomocí vývojářských nástrojů prověřte vygenerovaný kód. Část vygenerovaného kódu HTML je zobrazena níže:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Odvolání formátu pro sadu [Směrování](xref:mvc/controllers/routing) v souboru *Startup.cs* :

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core se překládá na `https://localhost:5001/Movies/Edit/4` požadavek `Edit` metody Action `Movies` kontroleru s parametrem `Id` 4. (Metody kontroleru se označují také jako metody akcí.)

[Pomocník značek](xref:mvc/views/tag-helpers/intro) je jednou z nejoblíbenějších nových funkcí v ASP.NET Core. Další informace najdete v tématu [Další zdroje](#additional-resources)informací.

<a name="get-post"></a>

Otevřete `Movies` kontroler a prověřte dvě `Edit` metody akce. Následující kód ukazuje `HTTP GET Edit` metodu, která načte film a naplní formulář pro úpravy vygenerovaný souborem *Edit. cshtml* Razor .

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

`[Bind]`Atribut je jedním ze způsobů, jak chránit před [vyúčtováním](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Do `[Bind]` atributu, který chcete změnit, byste měli zahrnout pouze vlastnosti. Další informace najdete v tématu [Chraňte svůj kontroler před vyúčtováním](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) poskytují alternativní přístup, který znemožňuje převzetí služeb při selhání.

Všimněte si, že druhá `Edit` Metoda Action předchází `[HttpPost]` atribut.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

`HttpPost`Atribut určuje, že tuto `Edit` metodu lze vyvolat *pouze* pro `POST` požadavky. Můžete použít `[HttpGet]` atribut na první metodu Edit, ale to není nutné, protože `[HttpGet]` je výchozí hodnota.

`ValidateAntiForgeryToken`Atribut slouží k [zabránění padělání požadavku](xref:security/anti-request-forgery) a je spárován s tokenem odolného proti padělání vygenerovaným v souboru zobrazení pro úpravy (views */Movies/Edit. cshtml*). Soubor pro úpravu zobrazení vygeneruje token proti padělání pomocí [pomocníka značky formuláře](xref:mvc/views/working-with-forms).

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Pomocník značek formuláře](xref:mvc/views/working-with-forms) generuje skrytý token proti padělání, který se musí shodovat s `[ValidateAntiForgeryToken]` generovaným tokenem odolného proti padělání v `Edit` metodě kontroleru filmů. Další informace najdete v tématu [padělání proti vyžádání](xref:security/anti-request-forgery).

`HttpGet Edit`Metoda vezme `ID` parametr videa, vyhledá film pomocí metody Entity Framework `FindAsync` a vrátí vybraný film do zobrazení pro úpravy. Pokud se video nenajde, `NotFound` vrátí se (HTTP 404).

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Když systém generování uživatelského rozhraní vytvořil zobrazení pro úpravy, zkontroloval `Movie` třídu a vytvořil kód pro vykreslení `<label>` a `<input>` prvky pro každou vlastnost třídy. Následující příklad ukazuje zobrazení pro úpravy, které bylo vygenerováno systémem pro generování uživatelského rozhraní sady Visual Studio:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Všimněte si, jak šablona zobrazení obsahuje `@model MvcMovie.Models.Movie` příkaz v horní části souboru. `@model MvcMovie.Models.Movie`Určuje, že zobrazení očekává, že model pro šablonu zobrazení bude typu `Movie` .

Generovaný kód používá několik pomocných metod značek pro zjednodušení značek HTML. [Pomocník značek Label](xref:mvc/views/working-with-forms) zobrazí název pole ("title", "ReleaseDate", "Žánr" nebo "Price"). [Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) VYKRESLUJE `<input>` prvek HTML. [Pomocník pro ověřování značek](xref:mvc/views/working-with-forms) zobrazí všechny ověřovací zprávy přidružené k této vlastnosti.

Spusťte aplikaci a přejděte na `/Movies` adresu URL. Klikněte na odkaz **Upravit** . V prohlížeči zobrazte zdroj stránky. Vygenerovaný kód HTML pro `<form>` element je uveden níže.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

`<input>`Prvky jsou v elementu, `HTML <form>` jehož `action` atribut je nastaven na hodnotu post na `/Movies/Edit/id` adresu URL. Data formuláře budou při kliknutí na tlačítko publikována na serveru `Save` . Poslední řádek před uzavíracím `</form>` prvkem zobrazuje skrytý token [XSRF](xref:security/anti-request-forgery) generovaný [pomocníkem značek formuláře](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Zpracovává se žádost POST.

Následující výpis zobrazuje `[HttpPost]` verzi `Edit` metody Action.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[ValidateAntiForgeryToken]`Atribut ověří skrytý token [XSRF](xref:security/anti-request-forgery) generovaný generátorem tokenů ochrany proti padělání v [pomocné rutině značky Form](xref:mvc/views/working-with-forms) .

Systém [vázání modelů](xref:mvc/models/model-binding) přebírá hodnoty v zaúčtovaném formuláři a vytvoří `Movie` objekt, který je předán jako `movie` parametr. `ModelState.IsValid`Metoda ověřuje, že data odeslaná ve formuláři lze použít k úpravě (úpravě nebo aktualizaci) `Movie` objektu. Pokud jsou data platná, uloží se. Aktualizované (upravené) filmové údaje jsou uloženy do databáze voláním `SaveChangesAsync` metody kontextu databáze. Po uložení dat kód přesměruje uživatele na `Index` metodu Action `MoviesController` třídy, která zobrazí kolekci filmů, včetně změn, které byly právě provedeny.

Předtím, než se formulář pošle na server, ověřování na straně klienta zkontroluje všechna pravidla ověřování v polích. Pokud dojde k chybám ověření, zobrazí se chybová zpráva a formulář se nepublikuje. Pokud je JavaScript zakázaný, nebudete mít ověřování na straně klienta, ale server detekuje odeslané hodnoty, které nejsou platné, a hodnoty formuláře se zobrazí znovu s chybovými zprávami. Později v tomto kurzu prověříme [ověřování modelu](xref:mvc/models/validation) podrobněji. [Pomocný modul pro ověření značky](xref:mvc/views/working-with-forms) v šabloně zobrazení */filmy/upravit. cshtml* se postará o zobrazení příslušných chybových zpráv.

![Upravit zobrazení: výjimka pro nesprávnou hodnotu ceny ABC, že cena pole musí být číslo. Výjimka pro nesprávnou hodnotu data vydání typu xyz stav zadejte platné datum.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Všechny `HttpGet` metody v kontroleru filmů následují podobně jako vzor. Získají filmový objekt (nebo seznam objektů v případě `Index` ) a předá do zobrazení objekt (model). `Create`Metoda předá do zobrazení prázdný objekt filmu `Create` . Všechny metody, které vytvářejí, upravují, odstraňují nebo jinak upravují data, jsou v `[HttpPost]` přetížení metody. Úprava dat v `HTTP GET` metodě je bezpečnostní riziko. Úpravy dat v `HTTP GET` metodě také porušují osvědčené postupy http a model [REST](http://rest.elkstein.org/) architektury, který určuje, že požadavky GET by neměly měnit stav aplikace. Jinými slovy, provádění operace GET by mělo být bezpečná operace, která nemá žádné vedlejší účinky a neupravuje vaše trvalá data.

## <a name="additional-resources"></a>Další zdroje

* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod k pomocníkům značek](xref:mvc/views/tag-helpers/intro)
* [Vytváření pomocníků se značkami](xref:mvc/views/tag-helpers/authoring)
* [Ochrana proti padělání požadavků](xref:security/anti-request-forgery)
* Chraňte svůj kontroler před [vyúčtováním](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Pomocná rutina značky formuláře](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky popisku](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky výběru](xref:mvc/views/working-with-forms)
* [Pomocná značka ověřovací značky](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Předchozí](working-with-sql.md) 
>  [Další](search.md)  
