---
title: Metody kontroleru a zobrazení v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak pracovat s metodami kontroleru, zobrazení a DataAnnotations v ASP.NET Core.
ms.author: riande
ms.date: 12/25/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 5984194194f14153aaa1e80df028bbaaf182e02b
ms.sourcegitcommit: 4e87712029de2aceb1cf2c52e9e3dda8195a5b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53382040"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a>Metody kontroleru a zobrazení v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Máme o dobrý začátek aplikace movie, ale v prezentaci není ideální. Nechceme zobrazíte čas (12:00:00 dop. na následujícím obrázku) a **ReleaseDate** by měla být dvě slova.

![Index zobrazení: Datum vydání je jedno slovo (bez mezer) a každý film datum vydání ukazuje čas 12: 00](working-with-sql/_static/m55.png)

Otevřít *Models/Movie.cs* a přidejte zvýrazněné řádky je uvedeno níže:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Probereme [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) v dalším kurzu. [Zobrazit](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) atribut určuje, co má být zobrazen pro název pole (v tomto případě "Datum vydání" místo "ReleaseDate"). [Datový typ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) atribut určuje typ dat (datum), takže se nezobrazí čas informací uložených v poli.

`[Column(TypeName = "decimal(18, 2)")]` Anotace dat se vyžaduje, aby správně můžete mapovat Entity Framework Core `Price` měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).

Přejděte `Movies` řadič a podržte ukazatel myši nad **upravit** odkaz zobrazíte cílové adrese URL.

![Okno prohlížeče pomocí myši nad odkaz pro úpravy a odkazem na adresu Url https://localhost:5001/Movies/Edit/5 se zobrazí](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

**Upravit**, **podrobnosti**, a **odstranit** odkazy jsou generované Core MVC ukotvení pomocné rutiny značky do *Views/Movies/Index.cshtml* souboru.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) umožňují, aby se kód na straně serveru v souborech Razor podílel na vytváření a vykreslování prvků HTML. Ve výše uvedeném kódu `AnchorTagHelper` dynamicky generuje kód HTML `href` hodnotu atributu z id metody a tras akce kontroleru. Použijete **zobrazit zdroj** v oblíbeném prohlížeči nebo použití vývojářských nástrojích prozkoumat generovaného kódu. Část generovaný kód HTML je zobrazena níže:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Odvolat formát [směrování](xref:mvc/controllers/routing) nastavit *Startup.cs* souboru:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

ASP.NET Core se přeloží `https://localhost:5001/Movies/Edit/4` do požadavku na `Edit` metody akce `Movies` řadiče s parametrem `Id` 4. (Metody kontroleru jsou také známé jako metody akce.)

[Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) jsou jedním z nejoblíbenějších nové funkce v ASP.NET Core. Zobrazit [další prostředky](#additional-resources) Další informace.

Otevřít `Movies` kontroleru a prozkoumejte dva `Edit` metody akce. Následující kód ukazuje `HTTP GET Edit` metodu, která načte videa a naplní vygenerovaný formulář pro úpravy *Edit.cshtml* souboru Razor.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Následující kód ukazuje `HTTP POST Edit` metodu, která zpracovává hodnoty odeslaných filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

Následující kód ukazuje `HTTP POST Edit` metodu, která zpracovává hodnoty odeslaných filmu:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[Bind]` Atribut je jedním ze způsobů pro ochranu před [over-pass-the účtování](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Vlastnosti v by měly obsahovat pouze `[Bind]` atribut, který chcete změnit. Zobrazit [chránit před útoky over-pass-the účtování kontrolér](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application) Další informace. [Modely ViewModels](http://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) poskytnout alternativní způsob zabráníte typu over-pass-the účtování.

Všimněte si, že druhá `Edit` předchází metody akce `[HttpPost]` atribut.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

`HttpPost` Atribut určuje, že to `Edit` může být vyvolána metoda *pouze* pro `POST` požadavky. Můžete použít `[HttpGet]` atribut na první upravit metodu, ale není nutné protože `[HttpGet]` je výchozí nastavení.

`ValidateAntiForgeryToken` Atribut se používá k [paděláním požadavku](xref:security/anti-request-forgery) a oblastí token proti padělání vygenerované v upravit soubor zobrazení (*Views/Movies/Edit.cshtml*). Upravit soubor zobrazení generuje token proti padělání s [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms).

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

[Pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms) generuje skryté tokenu proti zfalšování, která se musí shodovat `[ValidateAntiForgeryToken]` vygenerovat token proti padělání v `Edit` metody filmy kontroleru. Další informace najdete v tématu [ochrana proti padělání požadavků](xref:security/anti-request-forgery).

`HttpGet Edit` Metoda přebírá tento film `ID` parametr, vyhledá videa pomocí Entity Frameworku `SingleOrDefaultAsync` metoda a vrátí vybraného videa do zobrazení pro úpravy. Pokud nelze najít videa, `NotFound` (HTTP 404), je vrácena.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Když systém generování uživatelského rozhraní zobrazení pro úpravy, prozkoumat `Movie` třídy a vytvořený kód pro vykreslení `<label>` a `<input>` prvky pro každou vlastnost třídy. Následující příklad ukazuje zobrazení pro úpravy, které vygeneroval systém generování uživatelského rozhraní sady Visual Studio:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Všimněte si, jak se má zobrazit šablonu `@model MvcMovie.Models.Movie` příkazu v horní části souboru. `@model MvcMovie.Models.Movie` Určuje, že zobrazení očekává, že model pro zobrazení šablony, která má být typu `Movie`.

Automaticky generovaný kód používá několik metod pomocné rutiny značky pro zjednodušení značka jazyka HTML. - [Pomocné rutiny značky popisek](xref:mvc/views/working-with-forms) zobrazuje název pole ("Title", "ReleaseDate", "Žánr" nebo "Price"). [Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) vykreslí HTML `<input>` elementu. [Pomocné rutiny značky ověření](xref:mvc/views/working-with-forms) zobrazí všechny zprávy ověření přidružené k této vlastnosti.

Spusťte aplikaci a přejděte `/Movies` adresy URL. Klikněte na tlačítko **upravit** odkaz. V prohlížeči zobrazte zdroj stránky. Vygenerovaný kód HTML `<form>` element je uveden níže.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

`<input>` Prvky jsou v `HTML <form>` elementu jehož `action` atribut je nastaven na Odeslat požadavek POST `/Movies/Edit/id` adresy URL. Publikuje se data formuláře na serveru při `Save` po kliknutí na tlačítko. Poslední řádek před uzavírací značku `</form>` ukazuje skrytého elementu [XSRF](xref:security/anti-request-forgery) token generovaných [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Zpracování požadavku POST

Následující seznam ukazuje `[HttpPost]` verzi `Edit` metody akce.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

`[ValidateAntiForgeryToken]` Ověří skrytý atribut [XSRF](xref:security/anti-request-forgery) generovaných generátor tokenů proti padělání v tokenu [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms)

[Vazby modelu](xref:mvc/models/model-binding) systému vezme hodnoty odeslaného formuláře a vytvoří `Movie` objektu, který je předán jako `movie` parametru. `ModelState.IsValid` Metoda ověří, že odeslaná data do formuláře je možné upravit (úpravy nebo aktualizace) `Movie` objektu. Pokud jsou data platná je uložen. Data o filmech aktualizované (upravené) je uložen do databáze pomocí volání `SaveChangesAsync` metoda kontext databáze. Po uložení dat, kód přesměruje uživatele `Index` metody akce `MoviesController` třídu, která zobrazuje kolekce filmů, včetně pouze změny.

Před formuláře je odeslána na server, ověřování na straně klienta kontroluje všechny ověřovací pravidla pro pole. Pokud nejsou žádné chyby ověření, zobrazí se chybová zpráva a není publikování formuláře. Pokud jazyk JavaScript je zakázán, nebude mít ověřování na straně klienta, ale server rozpozná odeslaných hodnot, které nejsou platné a hodnot formuláře se zobrazí znovu, s chybovými zprávami. Později v tomto kurzu se Zaměřujeme [ověření modelu](xref:mvc/models/validation) podrobněji. [Pomocné rutiny značky ověření](xref:mvc/views/working-with-forms) v *Views/Movies/Edit.cshtml* zobrazení šablony se postará o zobrazení odpovídající chybové zprávy.

![Upravte zobrazení: Výjimka pro nesprávnou hodnotu cena abc uvádí, že pole cena musí být číslo. Výjimka pro nesprávnou hodnotu Datum vydání stavů xyz prosím zadejte platné datum.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Všechny `HttpGet` metody v kontroleru film podobné tvar. Dostanou video (nebo seznam objektů, v případě `Index`) a předána do zobrazení objektu (modelu). `Create` Metoda předává do prázdné video `Create` zobrazení. Všechny metody, které vytvořit, upravit, odstranit nebo jinak upravit data, proveďte `[HttpPost]` přetížení metody. Úpravy dat v `HTTP GET` metoda představuje bezpečnostní riziko. Úpravy dat v `HTTP GET` metoda také porušuje architektury a osvědčené postupy HTTP [REST](http://rest.elkstein.org/) vzor, který určuje, že požadavky GET by neměly měnit stav vaší aplikace. Jinými slovy provádění operace GET by měl být bezpečný provoz, který nemá žádné vedlejší účinky a nemění trvalá data.

## <a name="additional-resources"></a>Další zdroje

* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod do pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Autor pomocných rutin značek](xref:mvc/views/tag-helpers/authoring)
* [Ochrana proti padělání požadavků](xref:security/anti-request-forgery)
* Ochrana vašich kontroléru z [over-pass-the účtování.](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModels](http://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Pomocná rutina značky formuláře](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky vstupu](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky popisku](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky výběru](xref:mvc/views/working-with-forms)
* [Pomocná rutina značky ověření](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Předchozí](working-with-sql.md)
> [další](search.md)  
