---
title: 'Kurz: Přidání řazení, filtrování a stránkování – ASP.NET MVC s EF core'
description: V tomto kurzu přidáte funkce řazení, filtrování a stránkování na stránku Students Index. Vytvoříte také stránku, která provádí jednoduché seskupení.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 99bf9ed59b47e8fbba838b97c3e032b9808f6a94
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657134"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Kurz: Přidání řazení, filtrování a stránkování – ASP.NET MVC s EF core

V předchozím kurzu jste implementovali sadu webových stránek pro základní operace CRUD pro studentské entity. V tomto kurzu přidáte funkce řazení, filtrování a stránkování na stránku Students Index. Vytvoříte také stránku, která provádí jednoduché seskupení.

Následující obrázek znázorňuje, jak bude stránka vypadat, až budete hotovi. Záhlaví sloupců jsou odkazy, na které může uživatel kliknout a seřadit je podle tohoto sloupce. Opakovaným klepnutím na záhlaví sloupce přepínáte mezi vzestupným a sestupným pořadím řazení.

![Stránka indexu studentů](sort-filter-page/_static/paging.png)

V tomto kurzu jste:

> [!div class="checklist"]
> * Přidání odkazů řazení sloupců
> * Přidání vyhledávacího pole
> * Přidání stránkování do indexu studentů
> * Přidat stránkování do metody Index
> * Přidání stránkovacích odkazů
> * Vytvořit stránku Informace

## <a name="prerequisites"></a>Požadavky

* [Implementace funkcí CRUD](crud.md)

## <a name="add-column-sort-links"></a>Přidání odkazů řazení sloupců

Chcete-li přidat řazení na stránku Index `Index` studentů, změníte metodu ovladače Studenti a přidáte kód do zobrazení StudentIndex.

### <a name="add-sorting-functionality-to-the-index-method"></a>Přidání funkce řazení do metody Index

V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Tento kód obdrží `sortOrder` parametr z řetězce dotazu v adrese URL. Hodnota řetězce dotazu je poskytována ASP.NET Core MVC jako parametr metody akce. Parametr bude řetězec, který je buď "Název" nebo "Datum", volitelně následuje podtržítko a řetězec "desc" určit sestupné pořadí. Výchozí pořadí řazení je vzestupně.

Při prvním požadavku na stránku Index neexistuje žádný řetězec dotazu. Studenti jsou zobrazeny ve vzestupném pořadí podle příjmení, což je výchozí hodnota `switch` stanovená v případě pádu v prohlášení. Když uživatel klepne na hypertextový odkaz `sortOrder` záhlaví sloupce, je v řetězci dotazu uvedena příslušná hodnota.

Dva `ViewData` prvky (NameSortParm a DateSortParm) jsou používány zobrazením ke konfiguraci hypertextových odkazů záhlaví sloupce s příslušnými hodnotami řetězce dotazu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Tohle jsou ternární prohlášení. První z nich určuje, `sortOrder` že pokud je parametr null nebo prázdný, NameSortParm by měl být nastaven na "name_desc"; v opačném případě by měla být nastavena na prázdný řetězec. Tyto dva příkazy umožňují zobrazení nastavit hypertextové odkazy záhlaví sloupců takto:

|  Aktuální pořadí řazení  | Hypertextový odkaz příjmení | Hypertextový odkaz data |
|:--------------------:|:-------------------:|:--------------:|
| Příjmení vzestupně  | descending          | ascending      |
| Příjmení sestupně | ascending           | ascending      |
| Datum vzestupně       | ascending           | descending     |
| Datum sestupně      | ascending           | ascending      |

Metoda používá LINQ entity k určení sloupce, podle kterého se má řadit. Kód vytvoří `IQueryable` proměnnou před příkazem switch, upraví ji v příkazu switch a zavolá metodu `ToListAsync` za příkazem. `switch` Při vytváření a `IQueryable` úpravě proměnných není do databáze odeslán žádný dotaz. Dotaz není proveden, dokud převést `IQueryable` objekt do kolekce voláním `ToListAsync`metody, jako je například . Proto tento kód má za následek jeden dotaz, `return View` který není proveden až do příkazu.

Tento kód může získat podrobné s velkým počtem sloupců. [Poslední kurz v této sérii](advanced.md#dynamic-linq) ukazuje, jak psát kód, `OrderBy` který umožňuje předat název sloupce v proměnné řetězce.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Přidání hypertextových odkazů záhlaví sloupců do zobrazení Studentský index

Nahraďte kód v *zobrazení/Studenti/Index.cshtml*, s následujícím kódem přidat záhlaví sloupce hypertextové odkazy. Změněné řádky jsou zvýrazněny.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Tento kód používá `ViewData` informace ve vlastnostech k nastavení hypertextových odkazů s příslušnými hodnotami řetězce dotazu.

Spusťte aplikaci, vyberte kartu **Studenti** a kliknutím na záhlaví sloupců **Příjmení** a **Datum registrace** ověřte, zda řazení funguje.

![Studenty index stránky v pořadí názvů](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Přidání vyhledávacího pole

Chcete-li přidat filtrování na stránku Students Index, přidáte do zobrazení textové pole a `Index` tlačítko odeslat a provedete odpovídající změny v metodě. Textové pole vám umožní zadat řetězec, který chcete vyhledat v polích křestníjméno a příjmení.

### <a name="add-filtering-functionality-to-the-index-method"></a>Přidání funkce filtrování do metody Index

V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem (změny jsou zvýrazněny).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Přidali jste `searchString` parametr do `Index` metody. Hodnota hledaného řetězce je přijata z textového pole, které přidáte do zobrazení rejstříku. Přidali jste také do příkazu LINQ klauzule where, která vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec. Příkaz, který přidá klauzule where je spuštěna pouze v případě, že je hodnota hledat.

> [!NOTE]
> Zde voláte `Where` metodu `IQueryable` na objektu a filtr bude zpracován na serveru. V některých případech může `Where` být volání metody jako metoda rozšíření v kolekci v paměti. (Předpokládejme například, že `_context.Students` změníte odkaz na `DbSet` tak, aby namísto EF `IEnumerable` odkazuje na metodu úložiště, která vrací kolekci.) Výsledek by za normálních okolností byl stejný, ale v některých případech může být odlišný.
>
>Například implementace `Contains` rozhraní .NET Framework metody provádí porovnání rozlišování malých a velkých písmen ve výchozím nastavení, ale v SQL Server je to určeno nastavení řazení instance serveru SQL Server. Toto nastavení je výchozí pro nerozlišování velkých a malých písmen. Můžete volat `ToUpper` metodu, aby test explicitně malá a velká písmena: *Kde (s => s.LastName.ToUpper(). Contains(searchString.ToUpper())*. To by zajistilo, že výsledky zůstanou stejné, pokud změníte `IEnumerable` kód později použít úložiště, které vrátí kolekci namísto objektu. `IQueryable` (Při volání `Contains` metody na `IEnumerable` kolekci získáte implementaci rozhraní .NET Framework; `IQueryable` při volání na objekt, získáte implementaci zprostředkovatele databáze.) Pro toto řešení je však k dispozici snížení výkonu. Kód `ToUpper` by dal funkci do klauzule WHERE příkazu TSQL SELECT. To by zabránilo optimalizátoru používat index. Vzhledem k tomu, že SQL je většinou nainstalován `ToUpper` jako malá a velká písmena, je nejlepší se vyhnout kódu, dokud migrovat do úložiště dat rozlišování velkých a malých písmen.

### <a name="add-a-search-box-to-the-student-index-view"></a>Přidání vyhledávacího pole do zobrazení studentského indexu

V *části Zobrazení/Student/Index.cshtml*přidejte zvýrazněný kód bezprostředně před značku tabulky pro otevření, abyste vytvořili titulek, textové pole a tlačítko **Hledat.**

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Tento kód `<form>` používá [pomocníka pro označení](xref:mvc/views/tag-helpers/intro) k přidání vyhledávacího textového pole a tlačítka. Ve výchozím `<form>` nastavení odešle pomocná značka data formuláře s post, což znamená, že parametry jsou předávány v textu zprávy HTTP a nikoli v adrese URL jako řetězce dotazu. Když zadáte HTTP GET, data formuláře jsou předána v adrese URL jako řetězce dotazu, což umožňuje uživatelům záložku url. W3C pokyny doporučujeme použít GET, pokud akce nemá za následek aktualizaci.

Spusťte aplikaci, vyberte kartu **Studenti,** zadejte vyhledávací řetězec a kliknutím na Hledat ověřte, zda filtrování funguje.

![Stránka indexu studentů s filtrováním](sort-filter-page/_static/filtering.png)

Všimněte si, že adresa URL obsahuje hledaný řetězec.

```html
http://localhost:5813/Students?SearchString=an
```

Pokud si tuto stránku přidáte do záložek, dostanete při použití záložky filtrovaný seznam. Přidání `method="get"` do `form` značky je to, co způsobilo, že řetězec dotazu byl generován.

V této fázi, pokud kliknete na odkaz řazení záhlaví sloupce, ztratíte hodnotu filtru, kterou jste zadali do pole **Hledat.** Opravíte to v další části.

## <a name="add-paging-to-students-index"></a>Přidání stránkování do indexu studentů

Chcete-li přidat stránku Studentova indexu, `PaginatedList` vytvoříte `Skip` třídu, která používá a `Take` příkazy k filtrování dat na serveru namísto toho, abyste vždy načítali všechny řádky tabulky. Potom provedete další změny `Index` v metodě a přidáte `Index` do zobrazení tlačítka stránkování. Následující obrázek znázorňuje tlačítka stránkování.

![Stránka indexu studentů s pagingovými odkazy](sort-filter-page/_static/paging.png)

Ve složce projektu `PaginatedList.cs`vytvořte a nahraďte kód šablony následujícím kódem.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

Metoda `CreateAsync` v tomto kódu přebírá velikost stránky a `Skip` číslo `Take` stránky `IQueryable`a použije příslušné příkazy pro . Když `ToListAsync` je volána na `IQueryable`, vrátí Seznam obsahující pouze požadovanou stránku. Vlastnosti `HasPreviousPage` `HasNextPage` a lze použít k povolení nebo zakázání **předchozí** a **další** stránkovací tlačítka.

Metoda `CreateAsync` se používá místo konstruktoru `PaginatedList<T>` k vytvoření objektu, protože konstruktory nelze spustit asynchronní kód.

## <a name="add-paging-to-index-method"></a>Přidat stránkování do metody Index

V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Tento kód přidá k podpisu metody parametr čísla stránky, aktuální parametr pořadí řazení a aktuální parametr filtru.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Při prvním zobrazení stránky nebo při kliknutí na stránkovací nebo seřazený odkaz má uživatel hodnotu null.  Pokud klepnete na odkaz stránkování, proměnná stránky bude obsahovat číslo stránky, které se má zobrazit.

Prvek `ViewData` s názvem CurrentSort poskytuje zobrazení s aktuální pořadí řazení, protože to musí být zahrnuty do stránkování odkazy, aby bylo možné zachovat pořadí řazení stejné při stránkování.

Prvek `ViewData` s názvem CurrentFilter poskytuje zobrazení s aktuálnířetězec filtru. Tato hodnota musí být zahrnuta do stránkovacích odkazů, aby bylo zachováno nastavení filtru během stránkování, a musí být obnovena do textového pole při opětovném zobrazení stránky.

Pokud se vyhledávací řetězec během stránkování změní, musí být stránka obnovena na hodnotu 1, protože nový filtr může vést k zobrazení různých dat. Hledaný řetězec se změní, když je do textového pole zadána hodnota a je stisknuto tlačítko Odeslat. V takovém případě `searchString` parametr není null.

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

Na konci `Index` metody `PaginatedList.CreateAsync` metoda převede studentdotaz na jednu stránku studentů v typu kolekce, která podporuje stránkování. Tato jediná stránka studentů je pak předána do zobrazení.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

Metoda `PaginatedList.CreateAsync` přebírá číslo stránky. Tyto dva otazníky představují operátor null-coalescing. Operátor null-coalescing definuje výchozí hodnotu pro typ s možnou hodnotou null; výraz `(pageNumber ?? 1)` znamená vrátit hodnotu, `pageNumber` pokud má hodnotu, `pageNumber` nebo vrátit 1, pokud je null.

## <a name="add-paging-links"></a>Přidání stránkovacích odkazů

V *části Zobrazení/Studenti/Index.cshtml*nahraďte existující kód následujícím kódem. Změny jsou zvýrazněny.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

Příkaz `@model` v horní části stránky určuje, že zobrazení `PaginatedList<T>` nyní získá `List<T>` objekt namísto objektu.

Odkazy záhlaví sloupce používají řetězec dotazu k předání aktuálního vyhledávacího řetězce řadiči, aby uživatel mohl řadit ve výsledcích filtru:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Tlačítka stránkování jsou zobrazena pomocníky značek:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Spusťte aplikaci a přejděte na stránku Studenti.

![Stránka indexu studentů s pagingovými odkazy](sort-filter-page/_static/paging.png)

Chcete-li zajistit, aby stránkování fungovalo, klepněte na stránkovací odkazy v různých pořadích řazení. Potom zadejte hledaný řetězec a zkuste stránkování znovu ověřit, že stránkování funguje také správně s řazením a filtrováním.

## <a name="create-an-about-page"></a>Vytvořit stránku Informace

Na stránce **Informace o** webu Univerzity Contoso zobrazíte, kolik studentů se zaregistrovalo pro každé datum registrace. To vyžaduje seskupení a jednoduché výpočty na skupinách. Chcete-li toho dosáhnout, proveďte následující kroky:

* Vytvořte třídu modelu zobrazení pro data, která je třeba předat do zobrazení.
* Vytvořte metodu O kontroleci.
* Vytvořte zobrazení Informace.

### <a name="create-the-view-model"></a>Vytvoření modelu pohledu

Vytvořte složku *SchoolViewModels* ve složce *Modely.*

V nové složce přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Úprava domácího ovladače

V *HomeController.cs*přidejte do horní části souboru následující příkazy pomocí:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Přidejte proměnnou třídy pro kontext databáze bezprostředně po otevření složené závorky pro třídu a získejte instanci kontextu z ASP.NET Core DI:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Přidejte `About` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

Příkaz LINQ seskupuje entity studenta podle data zápisu, vypočítá počet entit v každé `EnrollmentDateGroup` skupině a uloží výsledky do kolekce objektů modelu zobrazení.

### <a name="create-the-about-view"></a>Vytvoření zobrazení O informace

Přidejte soubor *Views/Home/About.cshtml* s následujícím kódem:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Spusťte aplikaci a přejděte na stránku Informace. Počet studentů pro každé datum zápisu se zobrazí v tabulce.

## <a name="get-the-code"></a>Získání kódu

[Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste:

> [!div class="checklist"]
> * Přidány odkazy na řazení sloupců
> * Přidáno vyhledávací pole
> * Přidáno stránkování do indexu studentů
> * Přidáno stránkování do metody Index
> * Přidány stránkovací odkazy
> * Vytvoření stránky Informace

Přejdete k dalšímu kurzu, kde se dozvíte, jak zpracovat změny datového modelu pomocí migrace.

> [!div class="nextstepaction"]
> [Další: Zpracování změn datového modelu](migrations.md)
