---
title: 'Kurz: Přidání řazení, filtrování a stránkování – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu přidáte na stránku indexu studentů funkce řazení, filtrování a stránkování. Vytvoří se také stránka, která provede jednoduché seskupení.
author: tdykstra
ms.author: tdykstra
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 4e52a3d3f56c4cf6f396ee9ff1c18061a2364c77
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583387"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Kurz: Přidání řazení, filtrování a stránkování – ASP.NET MVC pomocí EF Core

V předchozím kurzu jste implementovali sadu webových stránek pro základní operace CRUD pro entity studenta. V tomto kurzu přidáte na stránku indexu studentů funkce řazení, filtrování a stránkování. Vytvoří se také stránka, která provede jednoduché seskupení.

Na následujícím obrázku vidíte, jak stránka bude vypadat, až budete hotovi. Záhlaví sloupců jsou odkazy, na které může uživatel kliknout pro řazení podle daného sloupce. Kliknutí na záhlaví sloupce se opakovaně přepíná mezi vzestupném a sestupným řazením.

![Stránka indexu studentů](sort-filter-page/_static/paging.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat odkazy na řazení sloupců
> * Přidání vyhledávacího pole
> * Přidat stránkování do indexu studentů
> * Přidat stránkování do metody indexu
> * Přidat odkazy na stránkování
> * Vytvoření stránky o stránku

## <a name="prerequisites"></a>Požadavky

* [Implementace funkce CRUD](crud.md)

## <a name="add-column-sort-links"></a>Přidat odkazy na řazení sloupců

Chcete-li přidat řazení na stránku indexu studenta, změňte `Index` metodu kontroleru Students a přidejte kód do zobrazení indexu studenta.

### <a name="add-sorting-functionality-to-the-index-method"></a>Přidání funkcí řazení do metody index

V *StudentsController.cs*nahraďte `Index` metodu následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Tento kód obdrží `sortOrder` parametr z řetězce dotazu v adrese URL. Hodnota řetězce dotazu je poskytnuta ASP.NET Core MVC jako parametr metody Action. Parametr bude řetězec, který je buď "Name", nebo "date", volitelně následovaný podtržítkem a řetězcem "desc" pro určení sestupného pořadí. Výchozí pořadí řazení je vzestupné.

Při prvním vyžádání stránky indexu není k dispozici žádný řetězec dotazu. Studenti se zobrazí ve vzestupném pořadí podle příjmení, což je výchozí nastavení zavedené v `switch` případě příkazu. Když uživatel klikne na hypertextový odkaz záhlaví sloupce, `sortOrder` v řetězci dotazu je uvedena příslušná hodnota.

Tyto dva `ViewData` elementy (NameSortParm a DateSortParm) jsou používány zobrazením ke konfiguraci hypertextových odkazů záhlaví sloupce odpovídajícím hodnotám řetězce dotazu.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Jedná se o Ternární příkazy. První z nich určuje, že pokud `sortOrder` parametr má hodnotu null nebo je prázdný, NameSortParm by měl být nastaven na "name_desc"; v opačném případě by měl být nastaven na prázdný řetězec. Tyto dva příkazy umožňují zobrazení nastavit hypertextové odkazy záhlaví sloupce následujícím způsobem:

|  Aktuální pořadí řazení  | Hypertextový odkaz na poslední jméno | Hypertextový odkaz na datum |
|:--------------------:|:-------------------:|:--------------:|
| Příjmení vzestupné  | descending          | ascending      |
| Příjmení sestupně | ascending           | ascending      |
| Datum vzestupné       | ascending           | descending     |
| Datum sestupné      | ascending           | ascending      |

Metoda používá LINQ to Entities k určení sloupce, podle kterého se má řadit. Kód vytvoří `IQueryable` proměnnou před příkazem Switch, upraví ji v příkazu switch a `ToListAsync` zavolá metodu po `switch` příkazu. Při vytváření a úpravách `IQueryable` proměnných se do databáze neodesílají žádné dotazy. Dotaz není proveden, dokud neprovedete `IQueryable` převod objektu do kolekce voláním metody, jako je `ToListAsync`například. Proto tento kód má za následek jeden dotaz, který se neprovede až do `return View` příkazu.

Tento kód může získat podrobné zobrazení velkého počtu sloupců. [Poslední kurz v této sérii](advanced.md#dynamic-linq) ukazuje, jak napsat kód, který vám umožní předat název `OrderBy` sloupce v proměnné řetězce.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Přidat hypertextové odkazy záhlaví sloupce do zobrazení indexu studenta

Nahraďte kód v *zobrazeních/Students/index. cshtml*s následujícím kódem pro přidání hypertextových odkazů záhlaví sloupců. Změněné řádky jsou zvýrazněny.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Tento kód používá informace ve `ViewData` vlastnostech k nastavení hypertextových odkazů s odpovídajícími hodnotami řetězce dotazu.

Spusťte aplikaci, vyberte kartu **studenti** a kliknutím na záhlaví sloupce Datum **poslední jméno** a **Datum registrace** ověřte, že řazení funguje.

![Stránka indexu studentů v pořadí podle názvu](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Přidání vyhledávacího pole

Chcete-li přidat filtrování na stránku indexu studentů, přidejte do zobrazení textové pole a tlačítko Odeslat a proveďte odpovídající změny v `Index` metodě. Textové pole umožňuje zadat řetězec, který bude hledán v poli jméno a příjmení.

### <a name="add-filtering-functionality-to-the-index-method"></a>Přidání funkce filtrování do metody index

V *StudentsController.cs*nahraďte `Index` metodu následujícím kódem (změny jsou zvýrazněny).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Přidali `searchString` jste`Index` do metody parametr. Hodnota vyhledávacího řetězce je přijímána z textového pole, které přidáte do zobrazení index. Také jste přidali do příkazu LINQ klauzule WHERE, která vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec. Příkaz, který přidá klauzuli WHERE, je proveden pouze v případě, že existuje hodnota, která se má vyhledat.

> [!NOTE]
> Zde zavoláte `Where` metodu `IQueryable` na objekt a filtr bude zpracován na serveru. V některých scénářích může být `Where` metoda volání jako metoda rozšíření v kolekci v paměti. (Předpokládejme například, že změníte odkaz na `_context.Students` tak, aby místo EF `DbSet` odkazoval na metodu `IEnumerable` úložiště, která vrací kolekci.) Výsledek by byl normálně stejný, ale v některých případech se může lišit.
>
>Například .NET Framework implementace `Contains` metody provádí porovnání rozlišovat velká a malá písmena ve výchozím nastavení, ale v SQL Server to je určeno nastavením kolace instance SQL Server. Ve výchozím nastavení se nerozlišují malá a velká písmena. Můžete zavolat `ToUpper` metodu, aby test explicitně nerozlišovat velikost písmen:  *Kde (s = > s. LastName. ToUpper (). Obsahuje (searchString. ToUpper ())* . Aby bylo zajištěno, že výsledky zůstanou stejné, pokud později změníte kód, aby používal úložiště, které vrátí `IEnumerable` kolekci namísto `IQueryable` objektu. (Při volání `Contains` metody `IEnumerable` v kolekci získáte .NET Framework implementaci; při volání na `IQueryable` objekt získáte implementaci poskytovatele databáze.) Pro toto řešení však existuje snížení výkonu. `ToUpper` Kód by umístil funkci v klauzuli WHERE příkazu TSQL SELECT. Tím zabráníte Optimalizátoru v používání indexu. Vzhledem k tomu, že SQL je většinou nainstalován jako nerozlišovat velká a malá písmena, je `ToUpper` nejlepší vyhnout se kódu, dokud neprovedete migraci na úložiště dat citlivé na velká a malá písmena.

### <a name="add-a-search-box-to-the-student-index-view"></a>Přidání vyhledávacího pole do zobrazení indexu studenta

V *zobrazení/student/index. cshtml*přidejte zvýrazněný kód bezprostředně před levou značku tabulky, aby bylo možné vytvořit titulek, textové pole a tlačítko **hledání** .

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Tento kód používá `<form>` [pomocníka značek](xref:mvc/views/tag-helpers/intro) k přidání textového pole a tlačítka hledání. Ve výchozím nastavení `<form>` pomocník značek odesílá data formuláře pomocí příspěvku, což znamená, že parametry jsou předány v těle zprávy HTTP a nejsou v adrese URL jako řetězce dotazů. Když zadáte příkaz HTTP GET, data formuláře se předávají v adrese URL jako řetězce dotazů, které uživatelům umožňují záložku URL. Pokyny pro konsorcium W3C doporučují použít GET, když akce nevede k aktualizaci.

Spusťte aplikaci, vyberte kartu **Students** , zadejte hledaný řetězec a kliknutím na Hledat ověřte, že filtrování funguje.

![Stránka indexu studentů s filtrováním](sort-filter-page/_static/filtering.png)

Všimněte si, že adresa URL obsahuje hledaný řetězec.

```html
http://localhost:5813/Students?SearchString=an
```

Pokud tuto stránku zařadíte do záložky, zobrazí se při použití záložky filtrovaný seznam. `method="get"` Přidání`form` do značky je to, co způsobilo vygenerování řetězce dotazu.

Pokud v této fázi kliknete na odkaz seřadit záhlaví sloupce, ztratíte hodnotu filtru, kterou jste zadali do vyhledávacího pole . Vyřešíte to v další části.

## <a name="add-paging-to-students-index"></a>Přidat stránkování do indexu studentů

Chcete-li přidat stránkování na stránku indexu studentů, vytvoříte `PaginatedList` třídu, která používá `Skip` příkazy a `Take` k filtrování dat na serveru místo toho, aby se vždy načítala všechny řádky tabulky. Pak provedete další změny v `Index` metodě a přidáte `Index` do zobrazení tlačítka stránkování. Následující ilustrace znázorňuje stránkování tlačítek.

![Stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging.png)

Ve složce projektu vytvořte `PaginatedList.cs`a potom nahraďte kód šablony následujícím kódem.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

Metoda v tomto kódu má velikost stránky a číslo stránky a aplikuje příslušné `Skip` příkazy a `Take` na `IQueryable`. `CreateAsync` Když `ToListAsync` je volána `IQueryable`na, vrátí seznam obsahující pouze požadovanou stránku. Vlastnosti `HasPreviousPage` , `HasNextPage` které lze použít k povolení nebo zakázání tlačítek **předchozí** a **Další** stránkování.

Metoda se používá namísto konstruktoru k `PaginatedList<T>` vytvoření objektu, protože konstruktory nemůžou spouštět asynchronní kód. `CreateAsync`

## <a name="add-paging-to-index-method"></a>Přidat stránkování do metody indexu

V *StudentsController.cs*nahraďte `Index` metodu následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Tento kód přidá parametr čísla stránky, aktuální parametr pořadí řazení a aktuální parametr filtru na podpis metody.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Při prvním zobrazení stránky, nebo pokud uživatel neklikl na odkaz na stránkování nebo řazení, všechny parametry budou mít hodnotu null.  Pokud se klikne na odkaz na stránkování, proměnná stránky bude obsahovat číslo stránky, které se má zobrazit.

`ViewData` Element s názvem CurrentSort poskytuje zobrazení s aktuálním pořadím řazení, protože musí být součástí odkazů stránkování, aby bylo řazení stejné při stránkování.

`ViewData` Element s názvem CurrentFilter poskytuje zobrazení s aktuálním řetězcem filtru. Tato hodnota musí být součástí odkazů stránkování, aby bylo možné zachovat nastavení filtru během stránkování, a při zobrazení stránky musí být obnovena do textového pole.

Pokud se hledaný řetězec během stránkování změní, je nutné obnovit stránku na 1, protože nový filtr může mít za následek zobrazení různých dat. Hledaný řetězec se změní, když je v textovém poli vložena hodnota a stisknete tlačítko Odeslat. V takovém případě `searchString` parametr není null.

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

Na konci `Index` metody `PaginatedList.CreateAsync` převede metoda dotaz student na jednu stránku studentů v typu kolekce, který podporuje stránkování. Tato jediná strana studentů se pak předává do zobrazení.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

`PaginatedList.CreateAsync` Metoda přebírá číslo stránky. Dvě otazníky reprezentují operátor slučování s hodnotou null. Operátor slučování null definuje výchozí hodnotu pro typ s možnou hodnotou null. výraz `(pageNumber ?? 1)` znamená, že vrátí `pageNumber` hodnotu, pokud má hodnotu, nebo vrátí hodnotu 1, pokud `pageNumber` má hodnotu null.

## <a name="add-paging-links"></a>Přidat odkazy na stránkování

V *zobrazeních/Students/index. cshtml*nahraďte existující kód následujícím kódem. Změny jsou zvýrazněné.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

Příkaz v horní části stránky určuje, že zobrazení nyní `PaginatedList<T>` Získá objekt namísto `List<T>` objektu. `@model`

Záhlaví sloupce odkazuje pomocí řetězce dotazu k předání aktuálního vyhledávacího řetězce k řadiči, aby uživatel mohl seřadit výsledky filtru:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Tlačítka pro stránkování se zobrazují v pomocníkech značek:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Spusťte aplikaci a pokračujte na stránku students.

![Stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging.png)

Kliknutím na odkazy na stránkování v různých pořadích řazení zajistěte, aby stránkování fungovalo. Pak zadejte hledaný řetězec a zkuste znovu vytvořit stránkování, abyste ověřili, že stránkování funguje i správně s řazením a filtrováním.

## <a name="create-an-about-page"></a>Vytvoření stránky o stránku

Pro stránku se stránkou společnosti contoso na univerzitě se zobrazí, kolik studentů se zaregistrovalo pro každé datum registrace. To vyžaduje seskupování a jednoduché výpočty skupin. K tomu je třeba provést následující akce:

* Vytvořte třídu zobrazení modelu pro data, která potřebujete předat zobrazení.
* Vytvořte v domovském řadiči metodu About.
* Vytvořte zobrazení informace o.

### <a name="create-the-view-model"></a>Vytvoření modelu zobrazení

Vytvořte složku *SchoolViewModels* ve složce *modely* .

V nové složce přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Úprava domovského kontroleru

V *HomeController.cs*přidejte do horní části souboru následující příkazy using:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Přidejte proměnnou třídy pro kontext databáze hned za levou složenou závorku pro třídu a Získejte instanci kontextu z ASP.NET Core DI:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

`About` Přidejte metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

Příkaz LINQ seskupuje entity studenta podle data registrace, vypočítá počet entit v každé skupině a uloží výsledky do kolekce `EnrollmentDateGroup` objektů zobrazení modelu.

### <a name="create-the-about-view"></a>Vytvoření zobrazení o zobrazení

Přidejte *zobrazení/domů/o soubor. cshtml* pomocí následujícího kódu:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Spusťte aplikaci a pokračujte na stránku o produktu. V tabulce se zobrazí počet studentů pro každé datum zápisu.

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidané odkazy na řazení sloupců
> * Přidání vyhledávacího pole
> * Přidání stránkování do indexu studentů
> * Přidání stránkování do metody indexu
> * Přidání odkazů na stránkování
> * Vytvoření stránky o stránku

Přejděte k dalšímu kurzu, kde se dozvíte, jak zpracovávat změny datového modelu pomocí migrací.

> [!div class="nextstepaction"]
> [Generace Zpracování změn datového modelu](migrations.md)
