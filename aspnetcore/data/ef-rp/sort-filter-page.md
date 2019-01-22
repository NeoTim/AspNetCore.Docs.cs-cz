---
title: Stránky Razor s EF Core v ASP.NET Core – řazení, filtrování, stránkování – 3 z 8
author: rick-anderson
description: V tomto kurzu přidáte řazení, filtrování a stránkování funkce pro stránky ASP.NET Core a Entity Framework Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 350243fb94b4798293a5a61b580c3b3b4d8c6d4a
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444295"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>Stránky Razor s EF Core v ASP.NET Core – řazení, filtrování, stránkování – 3 z 8

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

Podle [Petr Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Jan Macek P](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

V tomto kurzu, řazení, filtrování, seskupování a stránkování se přidá funkce.

Následující obrázek ukazuje stránku dokončené. Záhlaví sloupců jsou odkazy, můžete kliknout na seřadí hodnoty ve sloupci. Kliknutím na záhlaví opakovaně sloupce Přepne mezi vzestupným a sestupným řazením.

![Studenti indexová stránka](sort-filter-page/_static/paging.png)

Pokud narazíte na potíže nelze vyřešit, stáhněte si [dokončené aplikace](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Přidání řazení na indexovou stránku

Přidejte do řetězce *Students/Index.cshtml.cs* `PageModel` tak, aby obsahovala řazení parametry:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Aktualizace *Students/Index.cshtml.cs* `OnGetAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Předchozí kód přijme `sortOrder` parametr z řetězce dotazu v adrese URL. Generuje adresu URL (včetně řetězce dotazu) [ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

`sortOrder` Je parametr "Name" nebo "Data". `sortOrder` Parametr může volitelně následovat "_desc" Zadejte sestupném pořadí. Je výchozí pořadí řazení vzestupně.

Když vyžádané indexovou stránku **studenty** propojení, neexistuje žádný řetězec dotazu. Studenty se zobrazují ve vzestupném pořadí podle jména. V vzestupném pořadí podle příjmení je výchozí nastavení (případ propuštěním) `switch` příkazu. Když uživatel klepne sloupce záhlaví odkaz, odpovídající `sortOrder` v hodnotě řetězce dotazu je zadaná hodnota.

`NameSort` a `DateSort` se použít ke konfiguraci hypertextové odkazy záhlaví sloupce s hodnotami řetězec dotazu odpovídající stránky Razor:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Následující kód obsahuje podmíněné C# [?: – operátor](/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

Určuje, že první řádek `sortOrder` má hodnotu null nebo je prázdný, `NameSort` je nastavena na "name_desc." Pokud `sortOrder` je **není** hodnotu null nebo je prázdný, `NameSort` je nastaven na prázdný řetězec.

`?: operator` Se taky říká tříhodnotový operátor.

Tyto dva příkazy povolit na stránce nastavení sloupce záhlaví hypertextové odkazy následujícím způsobem:

| Aktuální pořadí řazení | Poslední název hypertextový odkaz | Datum hypertextový odkaz |
|:--------------------:|:-------------------:|:--------------:|
| Poslední název vzestupně | descending        | ascending      |
| Poslední název sestupně | ascending           | ascending      |
| Datum vzestupně       | ascending           | descending     |
| Datum sestupně      | ascending           | ascending      |

Metoda používá k určení tento sloupec seřadit podle technologie LINQ to Entities. Inicializuje kód `IQueryable<Student>` před příkazem switch a změní v příkazu switch:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Když`IQueryable` vytvoření nebo úpravě, žádný dotaz odeslán do databáze. Dotaz není spuštěn až `IQueryable` objekt je převést do kolekce. `IQueryable` převedou do kolekce voláním metody `ToListAsync`. Proto `IQueryable` kódu výsledky v jediném dotazu, který se spustí až do následujícího příkazu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` může získat podrobné s velkým množstvím řazení sloupců.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Přidat záhlaví sloupce hypertextové odkazy na studenta indexovou stránku

Nahraďte kód v *Students/Index.cshtml*, s následující zvýrazněný kód:

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Předchozí kód:

* Přidá odkazy `LastName` a `EnrollmentDate` záhlaví sloupců.
* Pomocí informací v `NameSort` a `DateSort` nastavit hypertextové odkazy pomocí aktuálních hodnot pořadí řazení.

K ověření funkčnosti řazení:

* Spusťte aplikaci a vyberte **studenty** kartu.
* Klikněte na tlačítko **příjmení**.
* Klikněte na tlačítko **datum registrace**.

Pokud chcete získat lepší přehled kódu:

* V *Students/Index.cshtml.cs*, nastavit zarážku na `switch (sortOrder)`.
* Přidat kukátko pro `NameSort` a `DateSort`.
* V *Students/Index.cshtml*, nastavit zarážku na `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Projděte skrze ladicí program.

## <a name="add-a-search-box-to-the-students-index-page"></a>Přidání vyhledávacího pole na studenty indexovou stránku

Chcete-li přidat filtrování pro studenty indexovou stránku:

* Textové pole a tlačítko pro odeslání se přidá do stránky Razor. Do textového pole poskytuje hledaný řetězec v první nebo poslední název.
* Model stránky v aktualizaci hodnotu textového pole.

### <a name="add-filtering-functionality-to-the-index-method"></a>Filtrování doplňují Index – metoda

Aktualizace *Students/Index.cshtml.cs* `OnGetAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Předchozí kód:

* Přidá `searchString` parametr `OnGetAsync` metody. Přijetí hledání řetězcovou hodnotu z textového pole, který je přidán v další části.
* Přidání příkazu LINQ `Where` klauzuli. `Where` Klauzule vybere pouze studenti, jejichž křestní jméno nebo příjmení vyskytuje hledaný řetězec. Pouze v případě, že je hodnota k vyhledání, je proveden příkaz LINQ.

Poznámka: Předchozí kód volá `Where` metodu `IQueryable` objekt a filtr zpracování na serveru. V některých případech může volat aplikaci `Where` metody jako metody rozšíření v kolekci v paměti. Předpokládejme například, že `_context.Students` změní z EF Core `DbSet` úložiště metody, která se vrátí `IEnumerable` kolekce. Výsledek by obvykle stejný, ale v některých případech může lišit.

Například rozhraní .NET Framework provádění `Contains` ve výchozím nastavení provádí porovnání velká a malá písmena. V systému SQL Server `Contains` rozlišování je určeno nastavení kolace instance systému SQL Server. SQL Server výchozí hodnoty na velká a malá písmena. `ToUpper` může volat provést test explicitně velká a malá písmena:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Předchozí kód zajistí, že výsledky jsou malá a velká písmena Pokud se změní kód určený `IEnumerable`. Když `Contains` je volán na `IEnumerable` kolekce, .NET Core, který se použije implementace. Když `Contains` je volán na `IQueryable` objektu se použije implementace databáze. Vrácení `IEnumerable` z úložiště může mít penality výkonu:

1. Všechny řádky jsou vráceny z Databázového serveru.
1. Filtr platí pro všechny řádky vrácené v aplikaci.

Je snížení výkonu pro volání `ToUpper`. `ToUpper` Kód přidá funkce v klauzuli WHERE příkazu TSQL SELECT. Přidání funkce zabraňuje Optimalizátor pomocí indexu. Vzhledem k tomu, že SQL je nainstalován jako velkých a malých písmen, je nejlepší je vyhnout `ToUpper` volat, pokud není potřeba.

### <a name="add-a-search-box-to-the-student-index-page"></a>Přidání vyhledávacího pole na studenta indexovou stránku

V *Pages/Students/Index.cshtml*, přidejte následující zvýrazněný kód k vytvoření **hledání** tlačítko a různé chrome.

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Předchozí kód používá `<form>` [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) přidáte textové pole hledání a tlačítko. Ve výchozím nastavení `<form>` pomocné rutiny značky odešle formulář dat pomocí příspěvek. Příspěvku parametry jsou předány v textu zprávy HTTP a ne v adrese URL. Při použití HTTP GET data formuláře je předána v adrese URL jako řetězce dotazu. Předávání dat s řetězci dotazu umožňuje uživatelům adresa URL záložky. [W3C pokyny](https://www.w3.org/2001/tag/doc/whenToUseGet.html) doporučujeme, aby GET má být použit při akci nevede k aktualizaci.

Testování aplikace:

* Vyberte **studenty** kartě a zadejte hledaný řetězec.
* Vyberte **hledání**.

Všimněte si, že adresa URL vyskytuje hledaný řetězec.

```html
http://localhost:5000/Students?SearchString=an
```

Pokud je vytvořili záložku na stránce, na záložku obsahuje adresu URL stránky a `SearchString` řetězec dotazu. `method="get"` v `form` značka je, co způsobilo řetězec dotazu, který nevygeneruje.

V současné době vyberete řazení odkaz záhlaví sloupce filtru hodnotu **hledání** pole se ztratí. V další části má pevnou hodnotu ztráty filtru.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Přidání funkce stránkování na studenty indexovou stránku

V této části `PaginatedList` je vytvořená třída pro podporu stránkování. `PaginatedList` Třídy používá `Skip` a `Take` příkazy k filtrování dat na serveru, místo získávání všech řádků v tabulce. Následující obrázek znázorňuje tlačítka stránkování.

![Studenti index stránky s odkazy stránkování](sort-filter-page/_static/paging.png)

Ve složce projektu vytvořit `PaginatedList.cs` následujícím kódem:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

`CreateAsync` Metoda v předchozím kódu má velikost stránky a číslo stránky a použije příslušné `Skip` a `Take` příkazy `IQueryable`. Když `ToListAsync` je volán na `IQueryable`, vrací seznam obsahující pouze požadované stránky. Vlastnosti `HasPreviousPage` a `HasNextPage` slouží k povolení nebo zakázání **předchozí** a **Další** tlačítka stránkování.

`CreateAsync` Metoda se používá k vytvoření `PaginatedList<T>`. Nelze vytvořit konstruktor `PaginatedList<T>` objektu, konstruktory nelze spustit asynchronního kódu.

## <a name="add-paging-functionality-to-the-index-method"></a>Přidání funkce stránkování Index – metoda

V *Students/Index.cshtml.cs*, aktualizujte typ `Student` z `IList<Student>` k `PaginatedList<Student>`:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Aktualizace *Students/Index.cshtml.cs* `OnGetAsync` následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Předchozí kód přidá index stránky, aktuální `sortOrder`a `currentFilter` do podpisu metody.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Všechny parametry mají hodnotu null při:

* Na stránce funkce je volána z **studenty** odkaz.
* Uživatel nebyl kliknul stránkování a řazení odkaz.

Po kliknutí na odkaz stránkování indexovaná proměnná stránky obsahuje číslo stránky k zobrazení.

`CurrentSort` Stránka Razor poskytuje aktuální pořadí řazení. Aktuální pořadí řazení, musí být součástí odkazy stránkování zachovat pořadí řazení a stránkování.

`CurrentFilter` Stránka Razor poskytuje aktuální řetězec filtru. `CurrentFilter` Hodnotu:

* Musí být součástí odkazy stránkování pro zachování nastavení filtru během stránkování.
* Musíte do textového pole. až se obnoví na stránce se zobrazí znovu.

Pokud hledaný řetězec se změní při stránkování, stránky se resetuje na hodnotu 1. Na stránce musí obnovit na hodnotu 1, protože nový filtr může vést k jiné data se mají zobrazit. Pokud je zadána hodnota vyhledávání a **odeslat** je vybrán:

* Hledaný řetězec se změní.
* `searchString` Parametr není null.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync` Metoda převede student dotazu na jednu stránku studentů v typu kolekce, který podporuje stránkování. Této stránce studentů je předán stránky Razor.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Dvě otazníky v `PaginatedList.CreateAsync` představují [operátoru nulového sjednocení](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operátoru nulového sjednocení definuje výchozí hodnotu pro typ připouštějící hodnotu Null. Výraz `(pageIndex ?? 1)` znamená, že návratová hodnota z `pageIndex` Pokud má hodnotu. Pokud `pageIndex` nebude mít hodnotu, vrátí se 1.

## <a name="add-paging-links-to-the-student-razor-page"></a>Přidat odkazy stránkování na studenta stránky Razor

Aktualizovat kód v *Students/Index.cshtml*. Změny jsou zvýrazněny:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Odkazy záhlaví sloupce použijte řetězec dotazu k předání do aktuálního vyhledávaného řetězce `OnGetAsync` metodu tak, aby uživatel mohl třídit v rámci filtr výsledků:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Mají tlačítka stránkování podle pomocných rutin značek:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Spusťte aplikaci a přejděte na stránku pro studenty.

* Ujistěte se, že funguje stránkování, klikněte na odkazy stránkování v jiné pořadí řazení.
* K ověření, zda stránkování správně funguje s řazením a filtrováním, zadejte hledaný řetězec a stránkování.

![Studenti index stránky s odkazy stránkování](sort-filter-page/_static/paging.png)

Pokud chcete získat lepší přehled kódu:

* V *Students/Index.cshtml.cs*, nastavit zarážku na `switch (sortOrder)`.
* Přidat kukátko pro `NameSort`, `DateSort`, `CurrentSort`, a `Model.Student.PageIndex`.
* V *Students/Index.cshtml*, nastavit zarážku na `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Projděte skrze ladicí program.

## <a name="update-the-about-page-to-show-student-statistics"></a>Aktualizovat o stránky zobrazit statistiku studenta

V tomto kroku *Pages/About.cshtml* se aktualizuje a zobrazí, kolik studenty zaregistrovali pro každé datum registrace. Aktualizace používá seskupování a zahrnuje následující kroky:

* Vytvoření modelu zobrazení pro data používaná **o** stránky.
* Aktualizujte stránku o použití modelu zobrazení.

### <a name="create-the-view-model"></a>Vytvoření zobrazení modelu

Vytvoření *SchoolViewModels* složky *modely* složky.

V *SchoolViewModels* složky, přidejte *EnrollmentDateGroup.cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Aktualizace modelu o stránce

Webové šablony v ASP.NET Core 2.2 nezahrnují stránku o. Pokud používáte 2.2 technologie ASP.NET Core, vytvoření stránky Razor o.

Aktualizace *Pages/About.cshtml.cs* souboru následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

Příkaz LINQ skupiny studentů entity podle data registrace, vypočítá počet entit v každé skupině a ukládá výsledky v kolekci `EnrollmentDateGroup` zobrazit objekty modelu.

### <a name="modify-the-about-razor-page"></a>Upravit o stránky Razor

Nahraďte kód v *Pages/About.cshtml* souboru následujícím kódem:

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

Spusťte aplikaci a přejděte na stránku o. Počet studentů pro každé datum registrace se zobrazí v tabulce.

Pokud narazíte na potíže nelze vyřešit, stáhněte si [dokončené aplikace pro tuto fázi](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![O stránku](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Další zdroje

* [Ladění ASP.NET Core 2.x zdroje](https://github.com/aspnet/Docs/issues/4155)

V dalším kurzu se aplikace používá k aktualizaci modelu dat migrace.

::: moniker-end

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/crud)
> [další](xref:data/ef-rp/migrations)
