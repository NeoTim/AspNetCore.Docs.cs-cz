---
title: Razor Stránky s EF jádrem v ASP.NET jádru - Řazení, filtr, stránkování - 3 z 8
author: rick-anderson
description: V tomto kurzu přidáte funkce řazení, filtrování a stránkování na stránku Razor pomocí ASP.NET jádra a jádra frameworku entity.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 9563f3ef52ce429eb0a58b468acb8e9cd7b276e2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656462"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>Razor Stránky s EF jádrem v ASP.NET jádru - Řazení, filtr, stránkování - 3 z 8

[Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT), a Jon [P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Tento kurz přidá funkce řazení, filtrování a stránkování na stránky Studenti.

Následující obrázek znázorňuje dokončenou stránku. Záhlaví sloupců jsou odkazy, na které lze kliknout, aby bylo možné sloupec seřadit. Opakovaným klepnutím na záhlaví sloupce přepnete mezi vzestupným a sestupným pořadím řazení.

![Stránka indexu studentů](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Přidání řazení

Nahraďte kód v *Pages/Students/Index.cshtml.cs* následujícím kódem pro přidání řazení.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All&highlight=21-24,26,28-52)]

Předcházející kód:

* Přidá vlastnosti, které obsahují parametry řazení.
* Změní název vlastnosti `Student` `Students`na .
* Nahradí kód v `OnGetAsync` metodě.

Metoda `OnGetAsync` obdrží `sortOrder` parametr z řetězce dotazu v adrese URL. Adresa URL (včetně řetězce dotazu) je generována [pomocníkem pro značku ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).

Parametr `sortOrder` je buď "Název" nebo "Datum". Parametr `sortOrder` je volitelně následuje "_desc" pro určení sestupné pořadí. Výchozí pořadí řazení je vzestupně.

Když je požadována stránka Index z odkazu **Studenti,** neexistuje žádný řetězec dotazu. Studenti jsou zobrazeni ve vzestupném pořadí podle příjmení. Vzestupné pořadí podle příjmení je výchozí (pád-through `switch` případ) v příkazu. Když uživatel klepne na odkaz záhlaví `sortOrder` sloupce, příslušná hodnota je uvedena v hodnotě řetězce dotazu.

`NameSort`a `DateSort` jsou používány razor page ke konfiguraci záhlaví sloupce hypertextové odkazy s příslušnými hodnotami řetězce dotazu:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

Kód používá podmíněný operátor [C#?:](/dotnet/csharp/language-reference/operators/conditional-operator). Operátor `?:` je ternární operátor (trvá tři operandy). První řádek určuje, `sortOrder` že pokud je `NameSort` null nebo prázdný, je nastavena na "name_desc." Pokud `sortOrder` **není** null nebo `NameSort` prázdné, je nastavena na prázdný řetězec.

Tyto dva příkazy umožňují stránce nastavit hypertextové odkazy záhlaví sloupců takto:

| Aktuální pořadí řazení   | Hypertextový odkaz příjmení | Hypertextový odkaz data |
|:--------------------:|:-------------------:|:--------------:|
| Příjmení vzestupně  | descending          | ascending      |
| Příjmení sestupně | ascending           | ascending      |
| Datum vzestupně       | ascending           | descending     |
| Datum sestupně      | ascending           | ascending      |

Metoda používá LINQ entity k určení sloupce, podle kterého se má řadit. Kód inicializuje `IQueryable<Student>` před příkazem switch a upraví jej v příkazu switch:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Při`IQueryable` vytvoření nebo změně je do databáze odeslán žádný dotaz. Dotaz není proveden, `IQueryable` dokud objekt je převeden do kolekce. `IQueryable`jsou převedeny do kolekce voláním `ToListAsync`metody, jako je například . Proto výsledkem `IQueryable` kódu je jeden dotaz, který není proveden až do následujícího příkazu:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`může získat podrobné s velkým počtem seřaditelných sloupců. Informace o alternativní způsob, jak kód této funkce, najdete [v tématu použití dynamické LINQ pro zjednodušení kódu](xref:data/ef-mvc/advanced#dynamic-linq) ve verzi MVC této řady kurzů.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Přidání hypertextových odkazů záhlaví sloupců na stránku Studentský index

Nahraďte kód v *students/index.cshtml*následujícím kódem. Změny jsou zvýrazněny.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

Předcházející kód:

* Přidá hypertextové `LastName` odkazy `EnrollmentDate` k záhlaví a sloupců.
* Používá informace `NameSort` v `DateSort` aplikace a k nastavení hypertextových odkazů s aktuálními hodnotami pořadí řazení.
* Změní záhlaví stránky z Rejstříku na Studenty.
* Změny `Model.Student` `Model.Students`.

Ověření, že řazení funguje:

* Spusťte aplikaci a vyberte kartu **Studenti.**
* Klikněte na záhlaví sloupců.

## <a name="add-filtering"></a>Přidání filtrování

Přidání filtrování na stránku Students Index:

* Na stránku Razor se přidá textové pole a tlačítko odeslat. Textové pole poskytuje vyhledávací řetězec na jméno nebo příjmení.
* Model stránky se aktualizuje tak, aby používal hodnotu textového pole.

### <a name="update-the-ongetasync-method"></a>Aktualizace metody OnGetAsync

Nahraďte kód ve *students/index.cshtml.cs* následujícím kódem pro přidání filtrování:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=28,33,37-41)]

Předcházející kód:

* Přidá `searchString` parametr k `OnGetAsync` metodě a uloží hodnotu `CurrentFilter` parametru do vlastnosti. Hodnota hledaného řetězce je přijata z textového pole, které je přidáno v další části.
* Přidá do linq `Where` příkazu klauzule. Klauzule `Where` vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec. Linq příkaz je proveden pouze v případě, že je hodnota hledat.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable vs. IEnumerable

Kód volá `Where` metodu `IQueryable` na objekt u objektu a filtr je zpracován na serveru. V některých případech aplikace může `Where` volat metodu jako metodu rozšíření v kolekci v paměti. Předpokládejme `_context.Students` například změny `DbSet` z EF Core na `IEnumerable` metodu úložiště, která vrací kolekci. Výsledek by za normálních okolností byl stejný, ale v některých případech může být odlišný.

Například implementace rozhraní .NET `Contains` Framework provádí porovnání rozlišování velkých a malých písmen ve výchozím nastavení. V SQL `Contains` Server rozlišování velkých a malých písmen je určena řazení nastavení instance serveru SQL Server. SQL Server výchozí malá a velká písmena. SQLite výchozí rozlišování velkých a malých písmen. `ToUpper`může být volána, aby test explicitně malá a velká písmena:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

Předchozí kód by zajistil, že filtr nerozlišuje malá a velká písmena i v případě, že `Where` je metoda volána na `IEnumerable` nebo spuštěna na SQLite.

Když `Contains` je volána na `IEnumerable` kolekci, je použita implementace .NET Core. Když `Contains` je volána na `IQueryable` objekt, je použita implementace databáze.

Volání `Contains` na `IQueryable` je obvykle vhodnější z důvodů výkonu. Pomocí `IQueryable`služby filtrování provádí databázový server. Pokud `IEnumerable` je vytvořen jako první, všechny řádky musí být vráceny z databázového serveru.

Za volání je pokuta `ToUpper`za výkon. Kód `ToUpper` přidá funkci do klauzule WHERE příkazu TSQL SELECT. Přidaná funkce zabraňuje optimalizátoru používat index. Vzhledem k tomu, že SQL je nainstalován jako `ToUpper` malá a velká písmena, je nejlepší se vyhnout volání, když to není potřeba.

Další informace naleznete v tématu [Použití dotazu bez rozlišování velkých a malých písmen s poskytovatelem Sqlite](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-razor-page"></a>Aktualizace stránky Razor

Nahraďte kód v *Pages/Students/Index.cshtml* a vytvořte tlačítko **Hledat** a různé chromy.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

Předchozí kód používá `<form>` [pomocníka pro označení](xref:mvc/views/tag-helpers/intro) k přidání vyhledávacího textového pole a tlačítka. Ve výchozím `<form>` nastavení odešle pomocník značky data formuláře s post. S POST parametry jsou předány v textu zprávy HTTP a není v adrese URL. Při použití protokolu HTTP GET jsou data formuláře předána v adrese URL jako řetězce dotazu. Předávání dat pomocí řetězců dotazu umožňuje uživatelům záložku url. [W3C pokyny](https://www.w3.org/2001/tag/doc/whenToUseGet.html) doporučujeme GET by měl být použit, pokud akce nemá za následek aktualizaci.

Otestujte aplikaci:

* Vyberte kartu **Studenti** a zadejte hledaný řetězec. Pokud používáte SQLite, filtr nerozlišuje malá a `ToUpper` velká písmena pouze v případě, že jste implementovali volitelný kód zobrazený dříve.

* Vyberte **hledat**.

Všimněte si, že adresa URL obsahuje hledaný řetězec. Příklad:

```
https://localhost:<port>/Students?SearchString=an
```

Pokud je stránka označena záložkou, záložka obsahuje `SearchString` adresu URL stránky a řetězec dotazu. Ve `method="get"` značce `form` je to, co způsobilo, že řetězec dotazu má být generován.

V současné době, když je vybrán odkaz na řazení záhlaví sloupce, dojde ke ztrátě hodnoty filtru z pole **Hledat.** Hodnota ztraceného filtru je stanovena v další části.

## <a name="add-paging"></a>Přidání stránkování

V této části `PaginatedList` je vytvořena třída pro podporu stránkování. Třída `PaginatedList` používá `Skip` `Take` a příkazy filtrovat data na serveru namísto načítání všech řádků tabulky. Následující obrázek znázorňuje tlačítka stránkování.

![Stránka indexu studentů s pagingovými odkazy](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Vytvoření třídy PaginatedList

Ve složce projektu `PaginatedList.cs` vytvořte s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

Metoda `CreateAsync` v předchozím kódu přebírá velikost stránky a číslo `Skip` stránky `Take` a `IQueryable`použije příslušné příkazy pro . Když `ToListAsync` je volána na `IQueryable`, vrátí List obsahující pouze požadovanou stránku. Vlastnosti `HasPreviousPage` `HasNextPage` a slouží k povolení nebo zakázání **předchozí** a **další** stránkovací tlačítka.

Metoda `CreateAsync` se používá k `PaginatedList<T>`vytvoření . Konstruktor nemůže vytvořit `PaginatedList<T>` objekt; konstruktory nelze spustit asynchronní kód.

### <a name="add-paging-to-the-pagemodel-class"></a>Přidání stránkování do třídy PageModel

Nahraďte kód v *Students/Index.cshtml.cs* a přidejte stránkování.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=26,28-29,31,34-41,68-70)]

Předcházející kód:

* Změní typ vlastnosti `Students` `IList<Student>` z `PaginatedList<Student>`na .
* Přidá index stránky, `sortOrder`aktuální a `currentFilter` podpis `OnGetAsync` metody.
* Uloží pořadí řazení do vlastnosti CurrentSort.
* Obnoví index stránky na hodnotu 1, pokud je nový vyhledávací řetězec.
* Používá `PaginatedList` třídu k získání studentských entit.

Všechny parametry, `OnGetAsync` které obdrží, jsou null, pokud:

* Stránka je volána z odkazu **Studenti.**
* Uživatel neklikl na odkaz pro stránkování nebo řazení.

Po klepnutí na odkaz stránkování obsahuje proměnná indexu stránky číslo stránky, které se má zobrazit.

Vlastnost `CurrentSort` poskytuje Razor Page s aktuální pořadí řazení. Aktuální pořadí řazení musí být zahrnuto do stránkovacích odkazů, aby bylo pořadí řazení při stránkování zachováno.

Vlastnost `CurrentFilter` poskytuje Razor Page s aktuální řetězec filtru. Hodnota: `CurrentFilter`

* Musí být zahrnuty do stránkovacíodkazy, aby bylo zachováno nastavení filtru během stránkování.
* Při opětovném zobrazení stránky musí být obnovendo textového pole.

Pokud se vyhledávací řetězec během stránkování změní, stránka se obnoví na 1. Stránka musí být resetována na hodnotu 1, protože nový filtr může vést k zobrazení různých dat. Když je zadána vyhledávací hodnota a je vybrána možnost **Odeslat:**

  * Hledaný řetězec se změní.
  * Parametr `searchString` není null.

  Metoda `PaginatedList.CreateAsync` převede studentdotaz na jednu stránku studentů v typu kolekce, která podporuje stránkování. Tato jediná stránka studentů je předána na Stránku břitvy.

  Dva otazníky `pageIndex` po `PaginatedList.CreateAsync` ve volání představují [null-coalescing operátor](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operátor null-coalescing definuje výchozí hodnotu pro typ s možnou hodnotou null. Výraz `(pageIndex ?? 1)` znamená vrátit hodnotu, `pageIndex` pokud má hodnotu. Pokud `pageIndex` nemá hodnotu, vrátí 1.

### <a name="add-paging-links-to-the-razor-page"></a>Přidání stránkovacích odkazů na stránku Razor

Nahraďte kód v *Students/Index.cshtml* následujícím kódem. Změny jsou zvýrazněny:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Odkazy záhlaví sloupce používají řetězec dotazu k předání `OnGetAsync` aktuálního vyhledávacího řetězce metodě:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Tlačítka stránkování jsou zobrazena pomocníky značek:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Spusťte aplikaci a přejděte na stránku studentů.

* Chcete-li zajistit, aby stránkování fungovalo, klepněte na stránkovací odkazy v různých pořadích řazení.
* Chcete-li ověřit, zda stránkování funguje správně při řazení a filtrování, zadejte hledaný řetězec a zkuste stránkování.

![studenty index stránku s paging odkazy](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Přidat seskupení

Tato část vytvoří stránku Informace, která zobrazuje, kolik studentů se zaregistrovalo pro každé datum registrace. Aktualizace používá seskupení a zahrnuje následující kroky:

* Vytvořte model zobrazení pro data používaná stránkou **Informace.**
* Aktualizujte stránku Informace tak, aby používala model zobrazení.

### <a name="create-the-view-model"></a>Vytvoření modelu pohledu

Vytvořte složku *Models/SchoolViewModels.*

Vytvořte *soubor SchoolViewModels/EnrollmentDateGroup.cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-razor-page"></a>Vytvoření stránky Břitva

Vytvořte soubor *Pages/About.cshtml* s následujícím kódem:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Vytvoření modelu stránky

Vytvořte soubor *Pages/About.cshtml.cs* s následujícím kódem:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

Příkaz LINQ seskupuje entity studenta podle data zápisu, vypočítá počet entit v každé `EnrollmentDateGroup` skupině a uloží výsledky do kolekce objektů modelu zobrazení.

Spusťte aplikaci a přejděte na stránku Informace. Počet studentů pro každé datum zápisu se zobrazí v tabulce.

![O stránce](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>Další kroky

V dalším kurzu aplikace používá migrace k aktualizaci datového modelu.

> [!div class="step-by-step"]
> [Předchozí kurz](xref:data/ef-rp/crud)
> [Další kurz](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu je přidáno řazení, filtrování, seskupování a stránkování.

Následující obrázek znázorňuje dokončenou stránku. Záhlaví sloupců jsou odkazy, na které lze kliknout, aby bylo možné sloupec seřadit. Opakovaným klepnutím na záhlaví sloupce přepínáte mezi vzestupným a sestupným pořadím řazení.

![Stránka indexu studentů](sort-filter-page/_static/paging.png)

Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončenou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Přidání řazení na stránku Rejstřík

Přidejte řetězce *students/Index.cshtml.cs,* `PageModel` které obsahují parametry řazení:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Aktualizujte *studentskou stránku/index.cshtml.cs* `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

Předchozí kód obdrží `sortOrder` parametr z řetězce dotazu v adrese URL. Adresa URL (včetně řetězce dotazu) je generována [pomocníkem značky kotvy.](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

Parametr `sortOrder` je buď "Název" nebo "Datum". Parametr `sortOrder` je volitelně následuje "_desc" pro určení sestupné pořadí. Výchozí pořadí řazení je vzestupně.

Když je požadována stránka Index z odkazu **Studenti,** neexistuje žádný řetězec dotazu. Studenti jsou zobrazeni ve vzestupném pořadí podle příjmení. Vzestupné pořadí podle příjmení je výchozí (pád-through `switch` případ) v příkazu. Když uživatel klepne na odkaz záhlaví `sortOrder` sloupce, příslušná hodnota je uvedena v hodnotě řetězce dotazu.

`NameSort`a `DateSort` jsou používány razor page ke konfiguraci záhlaví sloupce hypertextové odkazy s příslušnými hodnotami řetězce dotazu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

Následující kód obsahuje c# podmíněný [?: operátor](/dotnet/csharp/language-reference/operators/conditional-operator):

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

První řádek určuje, `sortOrder` že pokud je `NameSort` null nebo prázdný, je nastavena na "name_desc." Pokud `sortOrder` **není** null nebo `NameSort` prázdné, je nastavena na prázdný řetězec.

Je `?: operator` také známý jako ternární operátor.

Tyto dva příkazy umožňují stránce nastavit hypertextové odkazy záhlaví sloupců takto:

| Aktuální pořadí řazení | Hypertextový odkaz příjmení | Hypertextový odkaz data |
|:--------------------:|:-------------------:|:--------------:|
| Příjmení vzestupně | descending        | ascending      |
| Příjmení sestupně | ascending           | ascending      |
| Datum vzestupně       | ascending           | descending     |
| Datum sestupně      | ascending           | ascending      |

Metoda používá LINQ entity k určení sloupce, podle kterého se má řadit. Kód inicializuje `IQueryable<Student>` před příkazem switch a upraví jej v příkazu switch:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Při`IQueryable` vytvoření nebo změně je do databáze odeslán žádný dotaz. Dotaz není proveden, `IQueryable` dokud objekt je převeden do kolekce. `IQueryable`jsou převedeny do kolekce voláním `ToListAsync`metody, jako je například . Proto výsledkem `IQueryable` kódu je jeden dotaz, který není proveden až do následujícího příkazu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync`může získat podrobné s velkým počtem seřaditelných sloupců.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Přidání hypertextových odkazů záhlaví sloupců na stránku Studentský index

Nahraďte kód ve *students/index.cshtml*následujícím zvýrazněným kódem:

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

Předcházející kód:

* Přidá hypertextové `LastName` odkazy `EnrollmentDate` k záhlaví a sloupců.
* Používá informace `NameSort` v `DateSort` aplikace a k nastavení hypertextových odkazů s aktuálními hodnotami pořadí řazení.

Ověření, že řazení funguje:

* Spusťte aplikaci a vyberte kartu **Studenti.**
* Klepněte na **položku Příjmení**.
* Klepněte na **položku Datum registrace**.

Chcete-li získat lepší pochopení kódu:

* V *souboru Students/Index.cshtml.cs*nastavte `switch (sortOrder)`zarážku na .
* Přidejte hodinky pro `NameSort` a `DateSort`.
* V *souboru Students/Index.cshtml*nastavte `@Html.DisplayNameFor(model => model.Student[0].LastName)`zarážku na .

Projděte ladicím programem.

## <a name="add-a-search-box-to-the-students-index-page"></a>Přidání vyhledávacího pole na stránku Studentský index

Přidání filtrování na stránku Students Index:

* Na stránku Razor se přidá textové pole a tlačítko odeslat. Textové pole poskytuje vyhledávací řetězec na jméno nebo příjmení.
* Model stránky se aktualizuje tak, aby používal hodnotu textového pole.

### <a name="add-filtering-functionality-to-the-index-method"></a>Přidání funkce filtrování do metody Index

Aktualizujte *studentskou stránku/index.cshtml.cs* `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Předcházející kód:

* Přidá `searchString` parametr k `OnGetAsync` metodě. Hodnota hledaného řetězce je přijata z textového pole, které je přidáno v další části.
* Přidáno do `Where` linq prohlášení klauzule. Klauzule `Where` vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec. Linq příkaz je proveden pouze v případě, že je hodnota hledat.

Poznámka: Předchozí kód volá `Where` metodu `IQueryable` na objektu a filtr je zpracován na serveru. V některých případech aplikace může `Where` volat metodu jako metodu rozšíření v kolekci v paměti. Předpokládejme `_context.Students` například změny `DbSet` z EF Core na `IEnumerable` metodu úložiště, která vrací kolekci. Výsledek by za normálních okolností byl stejný, ale v některých případech může být odlišný.

Například implementace rozhraní .NET `Contains` Framework provádí porovnání rozlišování velkých a malých písmen ve výchozím nastavení. V SQL `Contains` Server rozlišování velkých a malých písmen je určena řazení nastavení instance serveru SQL Server. SQL Server výchozí malá a velká písmena. `ToUpper`může být volána, aby test explicitně malá a velká písmena:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

Předchozí kód by zajistil, že výsledky jsou malá a `IEnumerable`velká písmena, pokud se kód změní na použití . Když `Contains` je volána na `IEnumerable` kolekci, je použita implementace .NET Core. Když `Contains` je volána na `IQueryable` objekt, je použita implementace databáze. Vrácení z `IEnumerable` úložiště může mít významný trest výkonu:

1. Všechny řádky jsou vráceny ze serveru DB.
1. Filtr se použije na všechny vrácené řádky v aplikaci.

Za volání je pokuta `ToUpper`za výkon. Kód `ToUpper` přidá funkci do klauzule WHERE příkazu TSQL SELECT. Přidaná funkce zabraňuje optimalizátoru používat index. Vzhledem k tomu, že SQL je nainstalován jako `ToUpper` malá a velká písmena, je nejlepší se vyhnout volání, když to není potřeba.

### <a name="add-a-search-box-to-the-student-index-page"></a>Přidání vyhledávacího pole na stránku Studentský index

Do *stránky/studenti/index.cshtml*přidejte následující zvýrazněný kód pro vytvoření tlačítka **Hledat** a různého chromu.

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

Předchozí kód používá `<form>` [pomocníka pro označení](xref:mvc/views/tag-helpers/intro) k přidání vyhledávacího textového pole a tlačítka. Ve výchozím `<form>` nastavení odešle pomocník značky data formuláře s post. S POST parametry jsou předány v textu zprávy HTTP a není v adrese URL. Při použití protokolu HTTP GET jsou data formuláře předána v adrese URL jako řetězce dotazu. Předávání dat pomocí řetězců dotazu umožňuje uživatelům záložku url. [W3C pokyny](https://www.w3.org/2001/tag/doc/whenToUseGet.html) doporučujeme GET by měl být použit, pokud akce nemá za následek aktualizaci.

Otestujte aplikaci:

* Vyberte kartu **Studenti** a zadejte hledaný řetězec.
* Vyberte **hledat**.

Všimněte si, že adresa URL obsahuje hledaný řetězec.

```html
http://localhost:5000/Students?SearchString=an
```

Pokud je stránka označena záložkou, záložka obsahuje `SearchString` adresu URL stránky a řetězec dotazu. Ve `method="get"` značce `form` je to, co způsobilo, že řetězec dotazu má být generován.

V současné době, když je vybrán odkaz na řazení záhlaví sloupce, dojde ke ztrátě hodnoty filtru z pole **Hledat.** Hodnota ztraceného filtru je stanovena v další části.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Přidání funkcí stránkování na stránku Studentindex

V této části `PaginatedList` je vytvořena třída pro podporu stránkování. Třída `PaginatedList` používá `Skip` `Take` a příkazy filtrovat data na serveru namísto načítání všech řádků tabulky. Následující obrázek znázorňuje tlačítka stránkování.

![Stránka indexu studentů s pagingovými odkazy](sort-filter-page/_static/paging.png)

Ve složce projektu `PaginatedList.cs` vytvořte s následujícím kódem:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

Metoda `CreateAsync` v předchozím kódu přebírá velikost stránky a číslo `Skip` stránky `Take` a `IQueryable`použije příslušné příkazy pro . Když `ToListAsync` je volána na `IQueryable`, vrátí List obsahující pouze požadovanou stránku. Vlastnosti `HasPreviousPage` `HasNextPage` a slouží k povolení nebo zakázání **předchozí** a **další** stránkovací tlačítka.

Metoda `CreateAsync` se používá k `PaginatedList<T>`vytvoření . Konstruktor nemůže vytvořit `PaginatedList<T>` objekt, konstruktory nelze spustit asynchronní kód.

## <a name="add-paging-functionality-to-the-index-method"></a>Přidání funkce stránkování do metody Index

V *části Students/Index.cshtml.cs*aktualizujte typ `Student` z do `IList<Student>` `PaginatedList<Student>`:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Aktualizujte *studentskou stránku/index.cshtml.cs* `OnGetAsync` pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

Předchozí kód přidá index stránky, `sortOrder`aktuální a `currentFilter` podpis metody.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Všechny parametry jsou null, pokud:

* Stránka je volána z odkazu **Studenti.**
* Uživatel neklikl na odkaz pro stránkování nebo řazení.

Po klepnutí na odkaz stránkování obsahuje proměnná indexu stránky číslo stránky, které se má zobrazit.

`CurrentSort`poskytuje Razor Page s aktuální pořadí řazení. Aktuální pořadí řazení musí být zahrnuto do stránkovacích odkazů, aby bylo pořadí řazení při stránkování zachováno.

`CurrentFilter`poskytuje Razor Page s aktuálnířetězec filtru. Hodnota: `CurrentFilter`

* Musí být zahrnuty do stránkovacíodkazy, aby bylo zachováno nastavení filtru během stránkování.
* Při opětovném zobrazení stránky musí být obnovendo textového pole.

Pokud se vyhledávací řetězec během stránkování změní, stránka se obnoví na 1. Stránka musí být resetována na hodnotu 1, protože nový filtr může vést k zobrazení různých dat. Když je zadána vyhledávací hodnota a je vybrána možnost **Odeslat:**

* Hledaný řetězec se změní.
* Parametr `searchString` není null.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

Metoda `PaginatedList.CreateAsync` převede studentdotaz na jednu stránku studentů v typu kolekce, která podporuje stránkování. Tato jediná stránka studentů je předána na Stránku břitvy.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Dva otazníky `PaginatedList.CreateAsync` v představují [null-coalescing operátor](/dotnet/csharp/language-reference/operators/null-conditional-operator). Operátor null-coalescing definuje výchozí hodnotu pro typ s možnou hodnotou null. Výraz `(pageIndex ?? 1)` znamená vrátit hodnotu, `pageIndex` pokud má hodnotu. Pokud `pageIndex` nemá hodnotu, vrátí 1.

## <a name="add-paging-links-to-the-student-razor-page"></a>Přidání stránkovacích odkazů na stránku razor studenta

Aktualizujte značky v *students/index.cshtml*. Změny jsou zvýrazněny:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Odkazy záhlaví sloupce používají řetězec dotazu k předání `OnGetAsync` aktuálního vyhledávacího řetězce metodě, aby uživatel mohl řadit ve výsledcích filtru:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Tlačítka stránkování jsou zobrazena pomocníky značek:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Spusťte aplikaci a přejděte na stránku studentů.

* Chcete-li zajistit, aby stránkování fungovalo, klepněte na stránkovací odkazy v různých pořadích řazení.
* Chcete-li ověřit, zda stránkování funguje správně při řazení a filtrování, zadejte hledaný řetězec a zkuste stránkování.

![studenty index stránku s paging odkazy](sort-filter-page/_static/paging.png)

Chcete-li získat lepší pochopení kódu:

* V *souboru Students/Index.cshtml.cs*nastavte `switch (sortOrder)`zarážku na .
* Přidejte hodinky `DateSort` `CurrentSort`pro `NameSort` `Model.Student.PageIndex`, , a .
* V *souboru Students/Index.cshtml*nastavte `@Html.DisplayNameFor(model => model.Student[0].LastName)`zarážku na .

Projděte ladicím programem.

## <a name="update-the-about-page-to-show-student-statistics"></a>Aktualizace stránky Informace zobrazí statistiky studentů

V tomto kroku se aktualizuje *pages/About.cshtml,* aby se zobrazilo, kolik studentů se zaregistrovalo pro každé datum zápisu. Aktualizace používá seskupení a zahrnuje následující kroky:

* Vytvořte model zobrazení pro data používaná stránkou **Informace.**
* Aktualizujte stránku Informace tak, aby používala model zobrazení.

### <a name="create-the-view-model"></a>Vytvoření modelu pohledu

Vytvořte složku *SchoolViewModels* ve složce *Modely.*

Ve složce *SchoolViewModels* přidejte *EnrollmentDateGroup.cs* s následujícím kódem:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Aktualizace modelu stránky Informace

Webové šablony v ASP.NET jádrem 2.2 neobsahují stránku Informace. Pokud používáte ASP.NET Core 2.2, vytvořte stránku O břitvě.

Aktualizujte soubor *Pages/About.cshtml.cs* pomocí následujícího kódu:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

Příkaz LINQ seskupuje entity studenta podle data zápisu, vypočítá počet entit v každé `EnrollmentDateGroup` skupině a uloží výsledky do kolekce objektů modelu zobrazení.

### <a name="modify-the-about-razor-page"></a>Změna stránky O břitvě

Nahraďte kód v souboru *Pages/About.cshtml* následujícím kódem:

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

Spusťte aplikaci a přejděte na stránku Informace. Počet studentů pro každé datum zápisu se zobrazí v tabulce.

Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončenou aplikaci pro tuto fázi](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![O stránce](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Další zdroje

* [Ladění ASP.NET zdroj Core 2.x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Verze tohoto kurzu pro YouTube](https://www.youtube.com/watch?v=MDs7PFpoMqI)

V dalším kurzu aplikace používá migrace k aktualizaci datového modelu.

> [!div class="step-by-step"]
> [Předchozí](xref:data/ef-rp/crud)
> [další](xref:data/ef-rp/migrations)

::: moniker-end

