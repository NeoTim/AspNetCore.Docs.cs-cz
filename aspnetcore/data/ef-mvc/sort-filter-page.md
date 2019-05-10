---
title: 'Kurz: Přidat řazení, filtrování a stránkování – ASP.NET MVC s EF Core'
description: V tomto kurzu přidáte řazení, filtrování a stránkování funkce pro studenty indexovou stránku. Také vytvoříte stránky, která provádí jednoduché seskupení.
author: rick-anderson
ms.author: tdykstra
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 921e27bf56587813f835357c9090c91a155c087b
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212546"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Kurz: Přidat řazení, filtrování a stránkování – ASP.NET MVC s EF Core

V předchozím kurzu jste implementovali sadu webových stránek pro základní operace CRUD pro studenty entity. V tomto kurzu přidáte řazení, filtrování a stránkování funkce pro studenty indexovou stránku. Také vytvoříte stránky, která provádí jednoduché seskupení.

Následující obrázek znázorňuje, co bude stránka vypadat až to budete mít. Záhlaví sloupců jsou odkazy, které může uživatel kliknout, chcete-li seřadit podle sloupce. Kliknutím na záhlaví opakovaně sloupce přepíná mezi vzestupným a sestupným řazením.

![Studenti indexová stránka](sort-filter-page/_static/paging.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat sloupec řazení odkazy
> * Přidání vyhledávacího pole
> * Přidání stránkování k indexu studentů
> * Přidání stránkování k Index – metoda
> * Přidat odkazy stránkování
> * Vytvoření stránky o

## <a name="prerequisites"></a>Požadavky

* [Implementace funkcí CRUD](crud.md)

## <a name="add-column-sort-links"></a>Přidat sloupec řazení odkazy

K přidání řazení Student indexovou stránku, změníte `Index` metody kontroleru studenty a přidejte kód k zobrazení indexu studentů.

### <a name="add-sorting-functionality-to-the-index-method"></a>Přidání funkce řazení Index – metoda

V *StudentsController.cs*, nahraďte `Index` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Tento kód přijme `sortOrder` parametr z řetězce dotazu v adrese URL. ASP.NET Core MVC poskytuje hodnotu řetězce dotazu jako parametr do metody akce. Parametr bude řetězec, který je buď "Name" nebo "Data", může volitelně následovat symbol podtržítka a řetězec "desc" Zadejte sestupném pořadí. Je výchozí pořadí řazení vzestupně.

Při prvním vyžádání stránky indexu není žádný řetězec dotazu. Studenty se zobrazují ve vzestupném pořadí podle příjmení, což je výchozí podle propuštěním případ `switch` příkazu. Když uživatel klikne na sloupce záhlaví hypertextový odkaz, odpovídající `sortOrder` v řetězci dotazu je zadaná hodnota.

Dvě `ViewData` prvky (NameSortParm a DateSortParm) se zobrazením používají ke konfiguraci hypertextové odkazy záhlaví sloupce s hodnotami řetězec dotazu odpovídající.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Jedná se o Ternární příkazy. První z nich určuje, že pokud `sortOrder` parametr má hodnotu null nebo prázdná, NameSortParm musí být nastavená na "name_desc"; v opačném případě by měla být nastavena na prázdný řetězec. Tyto dva příkazy Povolit zobrazení nastavte sloupec, hypertextové odkazy záhlaví následujícím způsobem:

|  Aktuální pořadí řazení  | Poslední název hypertextový odkaz | Datum hypertextový odkaz |
|:--------------------:|:-------------------:|:--------------:|
| Poslední název vzestupně  | descending          | ascending      |
| Poslední název sestupně | ascending           | ascending      |
| Datum vzestupně       | ascending           | descending     |
| Datum sestupně      | ascending           | ascending      |

Metoda používá k určení tento sloupec seřadit podle technologie LINQ to Entities. Kód vytvoří `IQueryable` proměnnou před příkazem switch ho změní v příkazu switch a volání `ToListAsync` za `switch` příkazu. Pokud při vytváření a úpravách `IQueryable` proměnné, žádný dotaz odeslán do databáze. Dotaz není spuštěn, dokud je převést `IQueryable` objektu do kolekce voláním metody `ToListAsync`. Proto tento kód výsledkem jednoho dotazu, který se spustí až `return View` příkazu.

Tento kód by mohl získat podrobné s velkým počtem sloupců. [V posledním kurzu této série](advanced.md#dynamic-linq) ukazuje, jak napsat kód, který umožňuje předat název `OrderBy` sloupce v proměnné typu řetězec.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Přidat záhlaví sloupce hypertextové odkazy do zobrazení indexu studenta

Nahraďte kód v *Views/Students/Index.cshtml*, následujícím kódem, jak přidat hypertextové odkazy záhlaví sloupce. Změněné řádky jsou zvýrazněné.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Tento kód používá informace v `ViewData` hodnoty vlastnosti, které chcete nastavit hypertextové odkazy s odpovídající dotaz řetězce.

Spusťte aplikaci, vyberte **studenty** kartu a klikněte na tlačítko **příjmení** a **datum registrace** záhlaví sloupce. Ověřte, že řazení funguje.

![Studenti indexovou stránku podle názvu](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Přidání vyhledávacího pole

Pokud chcete přidat, filtrování, abyste studenty indexovou stránku, budete přidat textové pole a tlačítko pro odeslání do zobrazení a provádět odpovídající změny v `Index` metody. Do textového pole umožňují zadat řetězec k vyhledání křestního jména a poslední název pole.

### <a name="add-filtering-functionality-to-the-index-method"></a>Filtrování doplňují Index – metoda

V *StudentsController.cs*, nahraďte `Index` – metoda (změny se zvýrazní) následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Přidání `searchString` parametr `Index` metody. Přijetí hledání řetězcovou hodnotu z textové pole, které přidáte k zobrazení indexu. Jste také přidali do příkazu LINQ where klauzuli, která vybere pouze studenti, jejichž křestní jméno nebo příjmení vyskytuje hledaný řetězec. Příkaz, který přidá where – klauzule provádí pouze v případě, že je hodnota k vyhledání.

> [!NOTE]
> Tady jsou volání `Where` metodu `IQueryable` objekt a Filtr zpracuje na serveru. V některých scénářích může být volání `Where` metody jako metody rozšíření v kolekci v paměti. (Například Předpokládejme, že změníte odkaz na `_context.Students` tak místo z EF `DbSet` odkazuje na metodu úložiště, která se vrátí `IEnumerable` kolekce.) Výsledek by obvykle stejný, ale v některých případech může lišit.
>
>Například rozhraní .NET Framework provádění `Contains` metoda provádí porovnání velká a malá písmena ve výchozím nastavení, ale v systému SQL Server se určuje podle nastavení kolace instance systému SQL Server. Toto nastavení výchozí hodnoty na velká a malá písmena. Lze zavolat `ToUpper` metoda provést test explicitně velká a malá písmena:  *Kde (s = > s.LastName.ToUpper(). Contains(searchString.ToUpper())*. Který zajistí, že výsledky zůstanou stejné, při změně kódu později použít úložiště, které vrátí `IEnumerable` kolekce místo `IQueryable` objektu. (Při volání `Contains` metodu `IEnumerable` kolekce, získat implementace rozhraní .NET Framework;. při jeho volání na `IQueryable` objektu, získáte implementace poskytovatele databáze.) Je však snížení výkonu pro toto řešení. `ToUpper` Vložili kódu funkce v klauzuli WHERE příkazu TSQL SELECT. Pomocí indexu, který by jinak znemožňovaly Optimalizátor. Vzhledem k tomu, že SQL většinou nainstalovaná jako velkých a malých písmen, je nejlepší je vyhnout `ToUpper` kód, dokud nemigrujete do úložiště dat malá a velká písmena.

### <a name="add-a-search-box-to-the-student-index-view"></a>Přidat vyhledávací pole k zobrazení indexu studenta

V *Views/Student/Index.cshtml*, přidejte zvýrazněný kód bezprostředně před zahájení Chcete-li vytvořit popisek, textové pole, značka tabulky a **hledání** tlačítko.

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Tento kód používá `<form>` [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) přidáte textové pole hledání a tlačítko. Ve výchozím nastavení `<form>` pomocné rutiny značky odešle formulář dat příspěvku, což znamená, že parametry jsou předány v textu zprávy HTTP a ne v adrese URL jako řetězce dotazu. Při zadávání HTTP GET data formuláře je předána v adrese URL jako řetězce dotazu, který uživatelům umožňuje adresa URL záložky. Doporučujeme pokyny W3C, abyste používali získáte akce nevede k aktualizaci.

Spusťte aplikaci, vyberte **studenty** kartu, zadejte vyhledávací řetězec a kliknutím na tlačítko Hledat ověřte, že filtrování funguje.

![Studenti indexovou stránku s filtrováním](sort-filter-page/_static/filtering.png)

Všimněte si, že adresa URL vyskytuje hledaný řetězec.

```html
http://localhost:5813/Students?SearchString=an
```

Pokud se označit tuto stránku záložkou, získáte při použití na záložku filtrovaný seznam. Přidání `method="get"` k `form` značka je, co způsobilo řetězec dotazu, který nevygeneruje.

V této fázi, pokud kliknete na sloupce záhlaví řazení odkaz ztratíte hodnota filtru, který jste zadali v **hledání** pole. Opravíte to v další části.

## <a name="add-paging-to-students-index"></a>Přidání stránkování k indexu studentů

Přidání stránkování k studenty indexovou stránku, vytvoříte `PaginatedList` třídu, která používá `Skip` a `Take` příkazy k filtrování dat na serveru, místo vždy načítání všech řádků v tabulce. Pak provede další změny v `Index` metoda a přidat tlačítka stránkování `Index` zobrazení. Následující obrázek znázorňuje tlačítka stránkování.

![Studenti index stránky s odkazy stránkování](sort-filter-page/_static/paging.png)

Ve složce projektu vytvořit `PaginatedList.cs`a potom nahraďte kód šablony následujícím kódem.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

`CreateAsync` Metoda v tomto kódu má velikost stránky a číslo stránky a použije příslušné `Skip` a `Take` příkazy `IQueryable`. Když `ToListAsync` je volán na `IQueryable`, vrátí seznam obsahující pouze požadované stránky. Vlastnosti `HasPreviousPage` a `HasNextPage` slouží k povolení nebo zakázání **předchozí** a **Další** tlačítka stránkování.

A `CreateAsync` metoda se používá namísto konstruktor k vytvoření `PaginatedList<T>` objektu, protože konstruktory nelze spustit asynchronního kódu.

## <a name="add-paging-to-index-method"></a>Přidání stránkování k Index – metoda

V *StudentsController.cs*, nahraďte `Index` metodu s následujícím kódem.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Tento kód přidá parametr číslo stránky, aktuální parametr pořadí řazení a aktuální parametr filtru do podpisu metody.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

První stránka se zobrazí, nebo pokud uživatel nebyl kliknul stránkování a řazení odkaz, všechny parametry budou mít hodnotu null.  Pokud dojde ke kliknutí na odkaz stránkování, proměnnou stránky bude obsahovat číslo stránky k zobrazení.

`ViewData` Element s názvem CurrentSort poskytuje zobrazení s aktuální pořadí řazení, protože to musí obsahovat odkazy stránkování aby bylo možné zachovat pořadí řazení, při stránkování stejné.

`ViewData` Element s názvem CurrentFilter poskytuje zobrazení s aktuální řetězec filtru. Tato hodnota musí být součástí odkazy stránkování, aby byla zachována nastavení filtru během stránkování a je nutné do textového pole. až se obnoví na stránce se zobrazí znovu.

Pokud hledaný řetězec se změní při stránkování, stránky se musí resetovat na hodnotu 1, protože nový filtr může vést k zobrazení různých datových. Hledaný řetězec se změní, pokud je zadána hodnota v textovém poli a stisknutí tlačítka Odeslat. V takovém případě `searchString` parametr není null.

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

Na konci `Index` metody, `PaginatedList.CreateAsync` metoda převede student dotazu na jednu stránku studentů v typu kolekce, který podporuje stránkování. Této stránce studentů je pak předán zobrazení.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

`PaginatedList.CreateAsync` Metoda má číslo stránky. Dvě otazníky představují operátoru nulového sjednocení. Definuje výchozí hodnotu pro typ s možnou hodnotou Null; operátoru nulového sjednocení výraz `(pageNumber ?? 1)` znamená, že návratová hodnota z `pageNumber` Pokud má hodnotu, nebo vrátí 1, pokud `pageNumber` má hodnotu null.

## <a name="add-paging-links"></a>Přidat odkazy stránkování

V *Views/Students/Index.cshtml*, nahraďte existující kód následujícím kódem. Změny jsou zvýrazněné.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

`@model` Příkazu v horní části stránky určuje, že nyní získá zobrazení `PaginatedList<T>` místo objektu `List<T>` objektu.

Odkazy záhlaví sloupce použijte řetězec dotazu k předání aktuálního vyhledávaného řetězce kontroleru tak, aby uživatel mohl třídit v rámci filtr výsledků:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Mají tlačítka stránkování podle pomocných rutin značek:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Spusťte aplikaci a přejděte na stránku pro studenty.

![Studenti index stránky s odkazy stránkování](sort-filter-page/_static/paging.png)

Kliknutím na odkazy stránkování v jiné pořadí řazení pro Ujistěte se, že funguje stránkování. Potom zadejte hledaný řetězec a zkuste to znovu a ověřte, že stránkování také funguje správně s řazením a filtrováním stránkování.

## <a name="create-an-about-page"></a>Vytvoření stránky o

Pro web společnosti Contoso University **o** stránce bude zobrazovat, kolik studenty zaregistrovali pro každé datum registrace. To vyžaduje seskupování a jednoduché výpočtů na skupinách. K tomu budete postupujte takto:

* Vytvořte třídu modelu zobrazení dat, která je potřeba předat do zobrazení.
* Vytvořte metodu o v kontroler Home.
* Vytvořte zobrazení o.

### <a name="create-the-view-model"></a>Vytvoření zobrazení modelu

Vytvoření *SchoolViewModels* složky *modely* složky.

Nové složky, přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Upravit domovského Kontrolér

V *HomeController.cs*, přidejte následující příkazy using v horní části souboru:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Přidat proměnnou třídy kontextu databáze ihned po otevření složené závorky pro třídu a získání instance kontextu z ASP.NET Core DI:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Přidat `About` metodu s následujícím kódem:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

Příkaz LINQ skupiny studentů entity podle data registrace, vypočítá počet entit v každé skupině a ukládá výsledky v kolekci `EnrollmentDateGroup` zobrazit objekty modelu.

### <a name="create-the-about-view"></a>Vytvořit zobrazení

Přidat *Views/Home/About.cshtml* souboru následujícím kódem:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Spusťte aplikaci a přejděte na stránku o. Počet studentů pro každé datum registrace se zobrazí v tabulce.

## <a name="get-the-code"></a>Získat kód

[Stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidaný sloupec řazení odkazy
> * Přidání vyhledávacího pole
> * Přidání stránkování pro studenty indexu
> * Přidání stránkování k Index – metoda
> * Přidání odkazů stránkování
> * Vytvoří stránku o

Přejděte k dalšímu kurzu se naučíte zpracování změn datového modelu s použitím migrace.

> [!div class="nextstepaction"]
> [Další: Zpracování změn datových modelů](migrations.md)
