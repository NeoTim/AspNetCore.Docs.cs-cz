---
title: Razor Pages s EF Core v ASP.NET Core – řazení, filtrování, stránkování – 3 z 8
author: tdykstra
description: V tomto kurzu přidáte na stránku Razor funkce řazení, filtrování a stránkování pomocí ASP.NET Core a Entity Framework Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: b4cef98f3ad4973878c5fa65a47c0b86cdfc8686
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583517"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>Razor Pages s EF Core v ASP.NET Core – řazení, filtrování, stránkování – 3 z 8

Tím, že [Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Jan P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Tento kurz přináší na stránky studentů funkce řazení, filtrování a stránkování.

Na následujícím obrázku je znázorněna Dokončená stránka. Záhlaví sloupců jsou kliknutí na odkazy pro řazení sloupce. Chcete-li přepínat mezi vzestupném a sestupným řazením, klikněte na záhlaví sloupce opakovaně.

![Stránka indexu studentů](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Přidat řazení

Nahraďte kód na *stránkách/Students/index. cshtml. cs* následujícím kódem pro přidání řazení.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All&highlight=21-24,26,28-52)]

Předchozí kód:

* Přidá vlastnosti, které obsahují parametry řazení.
* Změní název `Student` vlastnosti na `Students`.
* Nahradí kód v `OnGetAsync` metodě.

`OnGetAsync` Metoda`sortOrder` přijímá parametr z řetězce dotazu v adrese URL. Adresa URL (včetně řetězce dotazu) je vygenerována [pomocníkem značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).

`sortOrder` Parametr je buď "Name" nebo "date". `sortOrder` Parametr je volitelně následován "_desc" a určuje sestupné pořadí. Výchozí pořadí řazení je vzestupné.

Když se na stránku indexu požaduje odkaz na **studenty** , neexistuje žádný řetězec dotazu. Studenti se zobrazí ve vzestupném pořadí podle příjmení. V `switch` příkazu je výchozí hodnota vzestupného pořadí podle příjmení (případ-až). Když uživatel klikne na odkaz záhlaví sloupce, je v `sortOrder` hodnotě řetězce dotazu uvedena příslušná hodnota.

`NameSort`a `DateSort` jsou používány stránkou Razor ke konfiguraci hypertextových odkazů záhlaví sloupce s příslušnými hodnotami řetězce dotazu:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Kód používá C# podmíněný operátor [?:](/dotnet/csharp/language-reference/operators/conditional-operator). `?:` Operátor je Ternární operátor (používá tři operandy). První řádek určuje, že pokud `sortOrder` je hodnota null nebo prázdná `NameSort` , je nastavena na "name_desc". Pokud `sortOrder` hodnota není null nebo prázdná, `NameSort` je nastavena na prázdný řetězec.

Tyto dva příkazy umožňují, aby stránka nastavila hypertextové odkazy záhlaví sloupce následujícím způsobem:

| Aktuální pořadí řazení   | Hypertextový odkaz na poslední jméno | Hypertextový odkaz na datum |
|:--------------------:|:-------------------:|:--------------:|
| Příjmení vzestupné  | descending          | ascending      |
| Příjmení sestupně | ascending           | ascending      |
| Datum vzestupné       | ascending           | descending     |
| Datum sestupné      | ascending           | ascending      |

Metoda používá LINQ to Entities k určení sloupce, podle kterého se má řadit. Kód inicializuje `IQueryable<Student>` před příkazem Switch a upraví ho v příkazu switch:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

`IQueryable` Při vytvoření nebo úpravě se do databáze neodesílají žádné dotazy. Dotaz není proveden, dokud `IQueryable` nebude objekt převeden do kolekce. `IQueryable`jsou převedeny na kolekci voláním metody, jako je `ToListAsync`. `IQueryable` Proto kód vytvoří jeden dotaz, který není proveden do následujícího příkazu:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`může získat podrobné zobrazení s velkým počtem sloupců, které lze seřadit. Informace o alternativním způsobu, jak tuto funkci zakódovat, najdete v tématu [použití dynamického LINQ ke zjednodušení kódu](xref:data/ef-mvc/advanced#dynamic-linq) ve verzi MVC této série kurzů.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Přidat hypertextové odkazy na záhlaví sloupce na stránku indexu studenta

Nahraďte kód v *Students/index. cshtml*s následujícím kódem. Změny jsou zvýrazněné.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Předchozí kód:

* Přidá hypertextové odkazy `LastName` do `EnrollmentDate` záhlaví sloupců a.
* Používá informace v `NameSort` a `DateSort` k nastavení hypertextových odkazů s aktuálními hodnotami pořadí řazení.
* Změní záhlaví stránky z indexu na studenty.
* Změny `Model.Student`v. `Model.Students`

Ověření, že řazení funguje:

* Spusťte aplikaci a vyberte kartu **Students** .
* Klikněte na záhlaví sloupců.

## <a name="add-filtering"></a>Přidat filtrování

Postup přidání filtrování na stránku indexu studentů:

* Do stránky Razor se přidá textové pole a tlačítko Odeslat. Textové pole poskytuje hledaný řetězec pro první nebo poslední název.
* Model stránky je aktualizován tak, aby používal hodnotu textového pole.

### <a name="update-the-ongetasync-method"></a>Aktualizace metody OnGetAsync

Nahraďte kód v *Students/index. cshtml. cs* následujícím kódem pro přidání filtrování:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=28,33,37-41)]

Předchozí kód:

* Přidá parametr do metody a uloží hodnotu parametru do `CurrentFilter` vlastnosti. `OnGetAsync` `searchString` Hodnota hledaného řetězce se přijímá z textového pole, které se přidalo v další části.
* Přidá do příkazu `Where` LINQ klauzuli. `Where` Klauzule vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec. Příkaz LINQ se spustí pouze v případě, že existuje hodnota, která se má vyhledat.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable vs. Rozhraní

Kód volá `Where` metodu `IQueryable` na objekt a filtr je zpracován na serveru. V některých scénářích může aplikace zavolat `Where` metodu jako metodu rozšíření v kolekci v paměti. Předpokládejme například, že `_context.Students` se změní z `DbSet` EF Core na metodu `IEnumerable` úložiště, která vrací kolekci. Výsledek by byl normálně stejný, ale v některých případech se může lišit.

Například implementace `Contains` .NET Framework ve výchozím nastavení provádí porovnání rozlišující malá a velká písmena. V SQL Server je `Contains` rozlišování velkých a malých písmen určeno nastavením kolace instance SQL Server. Výchozí hodnota SQL Server nerozlišuje malá a velká písmena. Výchozí hodnota SQLite rozlišuje velká a malá písmena. `ToUpper`může být volána, aby test explicitně nerozlišuje velikost písmen:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Předchozí kód zajistí, že filtr rozlišuje velká a malá písmena, i když `Where` je metoda volána `IEnumerable` na nebo spuštěná na sqlite.

Když `Contains` je volána `IEnumerable` v kolekci, je použita implementace .NET Core. Když `Contains` je volána `IQueryable` u objektu, je použita implementace databáze.

`Contains` Volání`IQueryable` na je obvykle vhodnější z důvodů výkonu. Pomocí `IQueryable`nástroje je filtrování provedeno databázovým serverem. Pokud je `IEnumerable` vytvořen jako první, všechny řádky musí být vráceny z databázového serveru.

Došlo ke snížení výkonu pro volání `ToUpper`. `ToUpper` Kód přidá funkci v klauzuli WHERE příkazu TSQL SELECT. Přidaná funkce brání Optimalizátoru v používání indexu. Vzhledem k tomu, že SQL je nainstalován jako nerozlišující velká a malá písmena, je `ToUpper` nejlepší vyhnout se volání, když není potřeba.

Další informace najdete v tématu [jak použít dotaz nerozlišující malá a velká písmena se zprostředkovatelem SQLite](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-razor-page"></a>Aktualizace stránky Razor

Nahraďte kód na *stránkách/Students/index. cshtml* a vytvořte tak tlačítko **hledání** a roztříděte Chrome.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Předchozí kód používá `<form>` [pomocníka značek](xref:mvc/views/tag-helpers/intro) k přidání textového pole a tlačítka hledání. Ve výchozím nastavení `<form>` pomocník značek odesílá data formuláře pomocí příspěvku. V případě příspěvku jsou parametry předány v těle zprávy HTTP a nikoli v adrese URL. Když se použije HTTP GET, data formuláře se předávají v adrese URL jako řetězce dotazu. Předání dat pomocí řetězců dotazů umožňuje uživatelům záložku adresy URL. Pokud akce nevede k aktualizaci, doporučuje se použít [pokyny pro W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) .

Testování aplikace:

* Vyberte kartu **Students** a zadejte hledaný řetězec. Pokud používáte SQLite, filtr rozlišuje velká a malá písmena pouze v případě, že jste implementovali volitelný `ToUpper` kód uvedený výše.

* Vyberte **Hledat**.

Všimněte si, že adresa URL obsahuje hledaný řetězec. Příklad:

```
https://localhost:<port>/Students?SearchString=an
```

Pokud je stránka záložkou, obsahuje záložka adresu URL stránky a `SearchString` řetězec dotazu. `method="get"` Veznačcejeto,cozpůsobilo`form` vygenerování řetězce dotazu.

V současné době je při výběru odkazu na řazení záhlaví sloupce ztracena hodnota filtru z **vyhledávacího** pole. Hodnota ztraceného filtru je opravena v následující části.

## <a name="add-paging"></a>Přidat stránkování

V této části `PaginatedList` je vytvořena třída pro podporu stránkování. Třída používá `Skip` příkazy a`Take` k filtrování dat na serveru místo načtení všech řádků tabulky. `PaginatedList` Následující ilustrace znázorňuje stránkování tlačítek.

![Stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Vytvoření třídy PaginatedList

Ve složce projektu vytvořte `PaginatedList.cs` pomocí následujícího kódu:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

Metoda v předchozím kódu má velikost stránky a číslo stránky a aplikuje příslušné `Skip` příkazy a `Take` na `IQueryable`. `CreateAsync` Když `ToListAsync` je volána `IQueryable`na, vrátí seznam obsahující pouze požadovanou stránku. Vlastnosti `HasPreviousPage` a `HasNextPage` slouží k povolení nebo zakázání tlačítek **předchozí** a **Další** stránkování.

Metoda slouží k `PaginatedList<T>`vytvoření. `CreateAsync` Konstruktor nemůže vytvořit `PaginatedList<T>` objekt. konstruktory nemohou spustit asynchronní kód.

### <a name="add-paging-to-the-pagemodel-class"></a>Přidat stránkování do třídy PageModel

Nahraďte kód v *Students/index. cshtml. cs* a přidejte stránkování.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=26,28-29,31,34-41,68-70)]

Předchozí kód:

* Změní typ `Students` vlastnosti z `IList<Student>` na `PaginatedList<Student>`.
* Přidá index stránky, aktuální `sortOrder` `currentFilter` a do `OnGetAsync` podpisu metody.
* Uloží pořadí řazení do vlastnosti CurrentSort.
* Obnoví index stránky na hodnotu 1, pokud je k dispozici nový hledaný řetězec.
* `PaginatedList` Používá třídu pro získání entit studenta.

Všechny parametry, které `OnGetAsync` obdrží, jsou null v těchto případech:

* Stránka je volána z odkazu **Students** .
* Uživatel nekliknul na odkaz na stránkování nebo řazení.

Po kliknutí na odkaz na stránkování obsahuje proměnná index stránky číslo stránky, která se má zobrazit.

`CurrentSort` Vlastnost poskytuje stránku Razor s aktuálním pořadím řazení. Aktuální pořadí řazení musí být ve stránkovacích odkazech zahrnuto, aby při stránkování zůstalo pořadí řazení.

`CurrentFilter` Vlastnost poskytuje stránku Razor s aktuálním řetězcem filtru. `CurrentFilter` Hodnota:

* Musí být součástí odkazů stránkování, aby bylo možné zachovat nastavení filtru během stránkování.
* Po zobrazení stránky se musí obnovit do textového pole.

Pokud se hledaný řetězec změní během stránkování, stránka je resetována na 1. Stránka musí být obnovena na 1, protože nový filtr může mít za následek zobrazení různých dat. Když je zadána hodnota vyhledávání a je vybrána možnost **Odeslat** :

  * Hledaný řetězec se změnil.
  * `searchString` Parametr není null.

  `PaginatedList.CreateAsync` Metoda převede dotaz studenta na jednu stránku studentů v typu kolekce, který podporuje stránkování. Tato jediná stránka studentů je předána na stránku Razor.

  Dvě otazníky po `pageIndex` `PaginatedList.CreateAsync` v volání reprezentují [operátor slučování null](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operátor slučování null definuje výchozí hodnotu pro typ s možnou hodnotou null. Výraz `(pageIndex ?? 1)` znamená, že vrátí `pageIndex` hodnotu, pokud má hodnotu. Pokud `pageIndex` hodnota nemá, vrátí hodnotu 1.

### <a name="add-paging-links-to-the-razor-page"></a>Přidat odkazy na stránkování na stránku Razor

Nahraďte kód v *Students/index. cshtml* následujícím kódem. Změny jsou zvýrazněny:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Záhlaví sloupce odkazuje pomocí řetězce dotazu k předání aktuálního vyhledávacího řetězce `OnGetAsync` metodě:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Tlačítka pro stránkování se zobrazují v pomocníkech značek:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Spusťte aplikaci a přejděte na stránku students.

* Chcete-li zajistit, aby stránkování fungovalo, klikněte na odkazy na stránkování v různých objednávkách řazení.
* Pokud chcete ověřit, že stránkování funguje správně s řazením a filtrováním, zadejte hledaný řetězec a zkuste použít stránkování.

![stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Přidat seskupení

Tato část obsahuje informace o stránce s informacemi o tom, kolik studentů bylo zaregistrováno pro každé datum zápisu. Tato aktualizace používá seskupení a obsahuje následující kroky:

* Vytvořte model zobrazení pro data používaná na stránce **About** .
* Aktualizujte stránku o, aby používala model zobrazení.

### <a name="create-the-view-model"></a>Vytvoření modelu zobrazení

Vytvořte *modely/složku SchoolViewModels* .

Vytvořte *SchoolViewModels/EnrollmentDateGroup. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-razor-page"></a>Vytvoření stránky Razor

Vytvořte *stránky/o souboru. cshtml* pomocí následujícího kódu:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Vytvoření modelu stránky

Vytvořte *stránky/o soubor. cshtml. cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

Příkaz LINQ seskupuje entity studenta podle data registrace, vypočítá počet entit v každé skupině a uloží výsledky do kolekce `EnrollmentDateGroup` objektů zobrazení modelu.

Spusťte aplikaci a přejděte na stránku o produktu. V tabulce se zobrazí počet studentů pro každé datum zápisu.

![O stránce](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Další postup

V dalším kurzu aplikace používá migrace k aktualizaci datového modelu.

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/crud)
> –[Další kurz](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se přidávají funkce řazení, filtrování, seskupování a stránkování.

Na následujícím obrázku je znázorněna Dokončená stránka. Záhlaví sloupců jsou kliknutí na odkazy pro řazení sloupce. Kliknutí na záhlaví sloupce se opakovaně přepíná mezi vzestupném a sestupným řazením.

![Stránka indexu studentů](sort-filter-page/_static/paging.png)

Pokud narazíte na problémy, které nemůžete vyřešit, stáhněte [dokončenou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Přidat řazení na stránku indexu

Přidejte řetězce do *studentů/index. cshtml. cs* `PageModel` , aby obsahovaly parametry řazení:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Aktualizujte *studenty/index. cshtml. cs* `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Předchozí kód obdrží `sortOrder` parametr z řetězce dotazu v adrese URL. Adresa URL (včetně řetězce dotazu) je vygenerována [pomocníkem značek ukotvení.](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

`sortOrder` Parametr je buď "Name" nebo "date". `sortOrder` Parametr je volitelně následován "_desc" a určuje sestupné pořadí. Výchozí pořadí řazení je vzestupné.

Když se na stránku indexu požaduje odkaz na **studenty** , neexistuje žádný řetězec dotazu. Studenti se zobrazí ve vzestupném pořadí podle příjmení. V `switch` příkazu je výchozí hodnota vzestupného pořadí podle příjmení (případ-až). Když uživatel klikne na odkaz záhlaví sloupce, je v `sortOrder` hodnotě řetězce dotazu uvedena příslušná hodnota.

`NameSort`a `DateSort` jsou používány stránkou Razor ke konfiguraci hypertextových odkazů záhlaví sloupce s příslušnými hodnotami řetězce dotazu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Následující kód obsahuje C# podmíněný [operátor?:](/dotnet/csharp/language-reference/operators/conditional-operator).

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

První řádek určuje, že pokud `sortOrder` je hodnota null nebo prázdná `NameSort` , je nastavena na "name_desc". Pokud `sortOrder` hodnota není null nebo prázdná, `NameSort` je nastavena na prázdný řetězec.

`?: operator` Je také označován jako Ternární operátor.

Tyto dva příkazy umožňují, aby stránka nastavila hypertextové odkazy záhlaví sloupce následujícím způsobem:

| Aktuální pořadí řazení | Hypertextový odkaz na poslední jméno | Hypertextový odkaz na datum |
|:--------------------:|:-------------------:|:--------------:|
| Příjmení vzestupné | descending        | ascending      |
| Příjmení sestupně | ascending           | ascending      |
| Datum vzestupné       | ascending           | descending     |
| Datum sestupné      | ascending           | ascending      |

Metoda používá LINQ to Entities k určení sloupce, podle kterého se má řadit. Kód inicializuje `IQueryable<Student>` před příkazem Switch a upraví ho v příkazu switch:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 `IQueryable` Při vytvoření nebo úpravě se do databáze neodesílají žádné dotazy. Dotaz není proveden, dokud `IQueryable` nebude objekt převeden do kolekce. `IQueryable`jsou převedeny na kolekci voláním metody, jako je `ToListAsync`. `IQueryable` Proto kód vytvoří jeden dotaz, který není proveden do následujícího příkazu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`může získat podrobné zobrazení s velkým počtem sloupců, které lze seřadit.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Přidat hypertextové odkazy na záhlaví sloupce na stránku indexu studenta

Nahraďte kód v *Students/index. cshtml*s následujícím zvýrazněným kódem:

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Předchozí kód:

* Přidá hypertextové odkazy `LastName` do `EnrollmentDate` záhlaví sloupců a.
* Používá informace v `NameSort` a `DateSort` k nastavení hypertextových odkazů s aktuálními hodnotami pořadí řazení.

Ověření, že řazení funguje:

* Spusťte aplikaci a vyberte kartu **Students** .
* Klikněte na **jméno a příjmení**.
* Klikněte na **Datum registrace**.

Chcete-li získat lepší informace o kódu:

* V nabídce *Students/index. cshtml. cs*nastavte zarážku `switch (sortOrder)`na.
* Přidejte kukátko pro `NameSort` a `DateSort`.
* V nabídce *Students/index. cshtml*nastavte zarážku `@Html.DisplayNameFor(model => model.Student[0].LastName)`na.

Projděte si ladicí program.

## <a name="add-a-search-box-to-the-students-index-page"></a>Přidání vyhledávacího pole na stránku indexu studentů

Postup přidání filtrování na stránku indexu studentů:

* Do stránky Razor se přidá textové pole a tlačítko Odeslat. Textové pole poskytuje hledaný řetězec pro první nebo poslední název.
* Model stránky je aktualizován tak, aby používal hodnotu textového pole.

### <a name="add-filtering-functionality-to-the-index-method"></a>Přidání funkce filtrování do metody index

Aktualizujte *studenty/index. cshtml. cs* `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Předchozí kód:

* `searchString` Přidá parametr`OnGetAsync` do metody. Hodnota hledaného řetězce se přijímá z textového pole, které se přidalo v další části.
* Přidáno do příkazu `Where` LINQ klauzule. `Where` Klauzule vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec. Příkaz LINQ se spustí pouze v případě, že existuje hodnota, která se má vyhledat.

Poznámka: Předchozí kód volá `Where` metodu `IQueryable` na objekt a filtr je zpracován na serveru. V některých scénářích může aplikace zavolat `Where` metodu jako metodu rozšíření v kolekci v paměti. Předpokládejme například, že `_context.Students` se změní z `DbSet` EF Core na metodu `IEnumerable` úložiště, která vrací kolekci. Výsledek by byl normálně stejný, ale v některých případech se může lišit.

Například implementace `Contains` .NET Framework ve výchozím nastavení provádí porovnání rozlišující malá a velká písmena. V SQL Server je `Contains` rozlišování velkých a malých písmen určeno nastavením kolace instance SQL Server. Výchozí hodnota SQL Server nerozlišuje malá a velká písmena. `ToUpper`může být volána, aby test explicitně nerozlišuje velikost písmen:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Předchozí kód zajistí, že výsledky budou nerozlišovat velká a malá písmena, pokud se kód změní `IEnumerable`na použití. Když `Contains` je volána `IEnumerable` v kolekci, je použita implementace .NET Core. Když `Contains` je volána `IQueryable` u objektu, je použita implementace databáze. `IEnumerable` Vrácení z úložiště může mít značnou sankci výkonu:

1. Všechny řádky jsou vráceny ze serveru databáze.
1. Filtr se použije na všechny vrácené řádky v aplikaci.

Došlo ke snížení výkonu pro volání `ToUpper`. `ToUpper` Kód přidá funkci v klauzuli WHERE příkazu TSQL SELECT. Přidaná funkce brání Optimalizátoru v používání indexu. Vzhledem k tomu, že SQL je nainstalován jako nerozlišující velká a malá písmena, je `ToUpper` nejlepší vyhnout se volání, když není potřeba.

### <a name="add-a-search-box-to-the-student-index-page"></a>Přidání vyhledávacího pole na stránku indexu studenta

Na *stránce Pages/Students/index. cshtml*přidejte následující zvýrazněný kód pro vytvoření tlačítka **hledání** a roztřídění Chrome.

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Předchozí kód používá `<form>` [pomocníka značek](xref:mvc/views/tag-helpers/intro) k přidání textového pole a tlačítka hledání. Ve výchozím nastavení `<form>` pomocník značek odesílá data formuláře pomocí příspěvku. V případě příspěvku jsou parametry předány v těle zprávy HTTP a nikoli v adrese URL. Když se použije HTTP GET, data formuláře se předávají v adrese URL jako řetězce dotazu. Předání dat pomocí řetězců dotazů umožňuje uživatelům záložku adresy URL. Pokud akce nevede k aktualizaci, doporučuje se použít [pokyny pro W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) .

Testování aplikace:

* Vyberte kartu **Students** a zadejte hledaný řetězec.
* Vyberte **Hledat**.

Všimněte si, že adresa URL obsahuje hledaný řetězec.

```html
http://localhost:5000/Students?SearchString=an
```

Pokud je stránka záložkou, obsahuje záložka adresu URL stránky a `SearchString` řetězec dotazu. `method="get"` Veznačcejeto,cozpůsobilo`form` vygenerování řetězce dotazu.

V současné době je při výběru odkazu na řazení záhlaví sloupce ztracena hodnota filtru z **vyhledávacího** pole. Hodnota ztraceného filtru je opravena v následující části.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Přidat funkci stránkování na stránku indexu studentů

V této části `PaginatedList` je vytvořena třída pro podporu stránkování. Třída používá `Skip` příkazy a`Take` k filtrování dat na serveru místo načtení všech řádků tabulky. `PaginatedList` Následující ilustrace znázorňuje stránkování tlačítek.

![Stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging.png)

Ve složce projektu vytvořte `PaginatedList.cs` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

Metoda v předchozím kódu má velikost stránky a číslo stránky a aplikuje příslušné `Skip` příkazy a `Take` na `IQueryable`. `CreateAsync` Když `ToListAsync` je volána `IQueryable`na, vrátí seznam obsahující pouze požadovanou stránku. Vlastnosti `HasPreviousPage` a `HasNextPage` slouží k povolení nebo zakázání tlačítek **předchozí** a **Další** stránkování.

Metoda slouží k `PaginatedList<T>`vytvoření. `CreateAsync` Konstruktor nemůže vytvořit `PaginatedList<T>` objekt, konstruktory nemůžou spustit asynchronní kód.

## <a name="add-paging-functionality-to-the-index-method"></a>Přidání funkce stránkování do metody index

V části *studenti/index. cshtml. cs*aktualizujte typ `Student` z `IList<Student>` na `PaginatedList<Student>`:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Aktualizujte *studenty/index. cshtml. cs* `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Předchozí kód přidá index stránky, aktuální `sortOrder` `currentFilter` a do signatury metody.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Všechny parametry mají hodnotu null, pokud:

* Stránka je volána z odkazu **Students** .
* Uživatel nekliknul na odkaz na stránkování nebo řazení.

Po kliknutí na odkaz na stránkování obsahuje proměnná index stránky číslo stránky, která se má zobrazit.

`CurrentSort`poskytuje stránku Razor s aktuálním pořadím řazení. Aktuální pořadí řazení musí být ve stránkovacích odkazech zahrnuto, aby při stránkování zůstalo pořadí řazení.

`CurrentFilter`poskytuje stránku Razor s aktuálním řetězcem filtru. `CurrentFilter` Hodnota:

* Musí být součástí odkazů stránkování, aby bylo možné zachovat nastavení filtru během stránkování.
* Po zobrazení stránky se musí obnovit do textového pole.

Pokud se hledaný řetězec změní během stránkování, stránka je resetována na 1. Stránka musí být obnovena na 1, protože nový filtr může mít za následek zobrazení různých dat. Když je zadána hodnota vyhledávání a je vybrána možnost **Odeslat** :

* Hledaný řetězec se změnil.
* `searchString` Parametr není null.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

`PaginatedList.CreateAsync` Metoda převede dotaz studenta na jednu stránku studentů v typu kolekce, který podporuje stránkování. Tato jediná stránka studentů je předána na stránku Razor.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Dvě otazníky v `PaginatedList.CreateAsync` reprezentují [operátor slučování s hodnotou null](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operátor slučování null definuje výchozí hodnotu pro typ s možnou hodnotou null. Výraz `(pageIndex ?? 1)` znamená, že vrátí `pageIndex` hodnotu, pokud má hodnotu. Pokud `pageIndex` hodnota nemá, vrátí hodnotu 1.

## <a name="add-paging-links-to-the-student-razor-page"></a>Přidat odkazy na stránkování na stránku pro studenty Razor

Aktualizujte značky v *Students/index. cshtml*. Změny jsou zvýrazněny:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Záhlaví sloupce odkazuje pomocí řetězce dotazu k předání aktuálního vyhledávacího řetězce `OnGetAsync` metodě, aby uživatel mohl seřadit výsledky filtru:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Tlačítka pro stránkování se zobrazují v pomocníkech značek:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Spusťte aplikaci a přejděte na stránku students.

* Chcete-li zajistit, aby stránkování fungovalo, klikněte na odkazy na stránkování v různých objednávkách řazení.
* Pokud chcete ověřit, že stránkování funguje správně s řazením a filtrováním, zadejte hledaný řetězec a zkuste použít stránkování.

![stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging.png)

Chcete-li získat lepší informace o kódu:

* V nabídce *Students/index. cshtml. cs*nastavte zarážku `switch (sortOrder)`na.
* Přidejte kukátko pro `NameSort`, `DateSort`, `CurrentSort` `Model.Student.PageIndex`a.
* V nabídce *Students/index. cshtml*nastavte zarážku `@Html.DisplayNameFor(model => model.Student[0].LastName)`na.

Projděte si ladicí program.

## <a name="update-the-about-page-to-show-student-statistics"></a>Aktualizace stránky o produktu pro zobrazení statistik studenta

V tomto kroku se aktualizují *stránky/o. cshtml* , aby se zobrazilo, kolik studentů bylo zaregistrované pro každé datum registrace. Tato aktualizace používá seskupení a obsahuje následující kroky:

* Vytvořte model zobrazení pro data používaná na stránce **About** .
* Aktualizujte stránku o, aby používala model zobrazení.

### <a name="create-the-view-model"></a>Vytvoření modelu zobrazení

Vytvořte složku *SchoolViewModels* ve složce *modely* .

Ve složce *SchoolViewModels* přidejte *EnrollmentDateGroup.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Aktualizace modelu stránky

Webové šablony v ASP.NET Core 2,2 neobsahují stránku About. Pokud používáte ASP.NET Core 2,2, vytvořte stránku about Razor.

Aktualizujte soubor *stránky/o. cshtml. cs* následujícím kódem:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

Příkaz LINQ seskupuje entity studenta podle data registrace, vypočítá počet entit v každé skupině a uloží výsledky do kolekce `EnrollmentDateGroup` objektů zobrazení modelu.

### <a name="modify-the-about-razor-page"></a>Úprava stránky se základními informacemi o Razor

Nahraďte kód v souboru *Pages/About. cshtml* následujícím kódem:

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

Spusťte aplikaci a přejděte na stránku o produktu. V tabulce se zobrazí počet studentů pro každé datum zápisu.

Pokud narazíte na problémy, které nemůžete vyřešit, Stáhněte si [dokončenou aplikaci pro tuto fázi](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![O stránce](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Další zdroje

* [Ladění zdrojového kódu ASP.NET Core 2. x](https://github.com/aspnet/AspNetCore.Docs/issues/4155)
* [Verze YouTube tohoto kurzu](https://www.youtube.com/watch?v=MDs7PFpoMqI)

V dalším kurzu aplikace používá migrace k aktualizaci datového modelu.

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/crud)Další
> [](xref:data/ef-rp/migrations)

::: moniker-end

