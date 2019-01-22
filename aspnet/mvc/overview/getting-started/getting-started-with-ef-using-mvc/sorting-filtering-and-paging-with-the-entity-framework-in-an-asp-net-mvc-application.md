---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Přidat řazení, filtrování a stránkování s Entity Framework v aplikaci ASP.NET MVC | Dokumentace Microsoftu'
author: tdykstra
description: V tomto kurzu přidáte řazení, filtrování a stránkování funkce, které **studenty** indexovou stránku. Můžete také vytvořit stránku jednoduché seskupení.
ms.author: riande
ms.date: 01/14/2019
ms.assetid: d5723e46-41fe-4d09-850a-e03b9e285bfa
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1f18a15d39d58ffb4ac48cfccee6519d33294e85
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444192"
---
# <a name="tutorial-add-sorting-filtering-and-paging-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Kurz: Přidat řazení, filtrování a stránkování s Entity Framework v aplikaci ASP.NET MVC

V [předchozí kurz o službě](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md), implementovat sadu webových stránek pro základní operace CRUD pro `Student` entity. V tomto kurzu přidáte řazení, filtrování a stránkování funkce, které **studenty** indexovou stránku. Můžete také vytvořit stránku jednoduché seskupení.

Následující obrázek ukazuje, co bude stránka vypadat až to budete mít. Záhlaví sloupců jsou odkazy, které může uživatel kliknout, chcete-li seřadit podle sloupce. Kliknutím na záhlaví opakovaně sloupce přepíná mezi vzestupným a sestupným řazením.

![Students_Index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat sloupec řazení odkazy
> * Přidání vyhledávacího pole
> * Přidání stránkování
> * Vytvoření stránky o

## <a name="prerequisites"></a>Požadavky

* [Implementace základních funkcí CRUD](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)

## <a name="add-column-sort-links"></a>Přidat sloupec řazení odkazy

K přidání řazení Student indexovou stránku, změníte `Index` metodu `Student` kontroleru a přidejte kód, který `Student` indexu zobrazení.

### <a name="add-sorting-functionality-to-the-index-method"></a>Přidat funkci řazení pro Index – metoda

- V *Controllers\StudentController.cs*, nahraďte `Index` metodu s následujícím kódem:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

Tento kód přijme `sortOrder` parametr z řetězce dotazu v adrese URL. ASP.NET MVC poskytuje hodnotu řetězce dotazu jako parametr do metody akce. Parametr je řetězec, který je buď "Name" nebo "Data", může volitelně následovat symbol podtržítka a řetězec "desc" Zadejte sestupném pořadí. Je výchozí pořadí řazení vzestupně.

Při prvním vyžádání stránky indexu není žádný řetězec dotazu. Studenty se zobrazují ve vzestupném pořadí podle `LastName`, což je výchozí podle propuštěním případ `switch` příkazu. Když uživatel klikne na sloupce záhlaví hypertextový odkaz, odpovídající `sortOrder` v řetězci dotazu je zadaná hodnota.

Dva `ViewBag` proměnné se používají tak, aby zobrazení můžete nakonfigurovat hypertextové odkazy záhlaví sloupce s hodnotami řetězec odpovídající dotaz:

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Jedná se o Ternární příkazy. První z nich určuje, že pokud `sortOrder` parametr má hodnotu null nebo prázdná, `ViewBag.NameSortParm` musí být nastavená na "název\_desc"; v opačném případě musí být nastavena na prázdný řetězec. Tyto dva příkazy Povolit zobrazení nastavte sloupec, hypertextové odkazy záhlaví následujícím způsobem:

| Aktuální pořadí řazení | Poslední název hypertextový odkaz | Datum hypertextový odkaz |
| --- | --- | --- |
| Poslední název vzestupně | descending | ascending |
| Poslední název sestupně | ascending | ascending |
| Datum vzestupně | ascending | descending |
| Datum sestupně | ascending | ascending |

Metoda používá [technologii LINQ to Entities](/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities) zadat Tento sloupec seřadit podle. Kód vytvoří <xref:System.Linq.IQueryable%601> proměnné před `switch` ho v příkazu, změní `switch` příkazu a volání `ToList` za `switch` příkaz. Pokud při vytváření a úpravách `IQueryable` proměnné, žádný dotaz odeslán do databáze. Dotaz není spuštěn, dokud je převést `IQueryable` objektu do kolekce voláním metody `ToList`. Proto tento kód výsledkem jednoho dotazu, který se spustí až `return View` příkazu.

Jako alternativu k psaní různých příkazů LINQ pro každé pořadí řazení můžete dynamicky vytvořit dotaz LINQ. Informace o dynamické LINQ, naleznete v tématu [dynamické LINQ](https://go.microsoft.com/fwlink/?LinkID=323957).

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Přidat záhlaví sloupce hypertextové odkazy do zobrazení indexu studenta

1. V *Views\Student\Index.cshtml*, nahraďte `<tr>` a `<th>` prvky pro řádek záhlaví s zvýrazněný kód:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=5-15)]

   Tento kód používá informace v `ViewBag` vlastnosti, které chcete nastavit hypertextové odkazy s odpovídající dotaz hodnoty řetězce.

2. Spuštění stránky a klikněte na tlačítko **příjmení** a **datum registrace** záhlaví sloupce. Ověřte, že řazení funguje.

   Po klepnutí **příjmení** záhlaví, studenti jsou zobrazena v sestupném pořadí poslední název.

## <a name="add-a-search-box"></a>Přidání vyhledávacího pole

Přidání filtrování na indexovou stránku studenty, kurzu přidáte textové pole a tlačítko pro odeslání do zobrazení a provádět odpovídající změny v `Index` metody. Textové pole umožňuje zadat řetězec k vyhledání křestního jména a poslední název pole.

### <a name="add-filtering-functionality-to-the-index-method"></a>Filtrování doplňují Index – metoda

- V *Controllers\StudentController.cs*, nahraďte `Index` – metoda (změny se zvýrazní) následujícím kódem:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=1,7-11)]

Kód přidá `searchString` parametr `Index` metody. Přijetí hledání řetězcovou hodnotu z textové pole, které přidáte k zobrazení indexu. Přidá také `where` klauzuli do příkazu LINQ, který vybere pouze studenti, jejichž křestní jméno nebo příjmení vyskytuje hledaný řetězec. Příkaz, který se přidá <xref:System.Linq.Queryable.Where%2A> klauzule spustí pouze v případě, že je hodnota k vyhledání.

> [!NOTE]
> V mnoha případech můžete volání stejné metody na sadu entit Entity Framework nebo jako rozšiřující metody na kolekci v paměti. Výsledky jsou obvykle stejné, ale v některých případech může lišit.
>
> Například rozhraní .NET Framework provádění `Contains` metoda vrátí všechny řádky, pokud předáte prázdný řetězec, ale poskytovateli rozhraní Entity Framework pro SQL Server Compact 4.0 vrátí nulový počet řádků prázdné řetězce. Proto kódem v příkladu (vložení `Where` výroku uvnitř `if` příkaz) zajišťuje, že získáte stejné výsledky pro všechny verze SQL serveru. Také, implementace rozhraní .NET Framework `Contains` metoda provádí porovnání velká a malá písmena ve výchozím nastavení, ale zprostředkovatele Entity Framework SQL Server provést porovnávání ve výchozím nastavení. Proto volání `ToUpper` metoda provést test explicitně velkých a malých písmen zajišťuje, že výsledky neměňte při změně kódu později použít úložiště, které vrátí `IEnumerable` kolekce místo `IQueryable` objektu. (Při volání `Contains` metodu `IEnumerable` kolekce, získat implementace rozhraní .NET Framework;. při jeho volání na `IQueryable` objektu, získáte implementace poskytovatele databáze.)
>
> Null zpracování může být také jiný poskytovatelé různých databází nebo při použití `IQueryable` objektu ve srovnání s při použití `IEnumerable` kolekce. Například v některých scénářích `Where` podmínka vyhodnocena jako `table.Column != 0` nemusí vrátit sloupce, které obsahují `null` jako hodnotu. Další informace najdete v tématu [nesprávné zpracování null proměnné v klauzuli where"](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl).

### <a name="add-a-search-box-to-the-student-index-view"></a>Přidat vyhledávací pole k zobrazení indexu studenta

1. V *Views\Student\Index.cshtml*, přidejte zvýrazněný kód bezprostředně před zahájením `table` tag, chcete-li vytvořit popisek, textové pole a **hledání** tlačítko.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=4-11)]

2. Spuštění stránky, zadejte vyhledávací řetězec a klikněte na tlačítko **hledání** k ověření, že filtrování funguje.

   Všimněte si, že adresa URL neobsahuje "k" hledaný řetězec, což znamená, že pokud označit tuto stránku záložkou, nezískáte filtrovaný seznam při použití na záložku. To platí také pro sloupec řazení odkazy, jak se budou řadit celý seznam. Změníte **hledání** tlačítka pomocí řetězců dotazu pro kritéria filtru v pozdější části kurzu.

## <a name="add-paging"></a>Přidání stránkování

Přidání stránkování na indexovou stránku studenty, začnete pomocí instalace **PagedList.Mvc** balíček NuGet. Pak provede další změny v `Index` metoda a přidejte odkazy stránkování na `Index` zobrazení. **PagedList.Mvc** je jednou z mnoha dobré stránkování a řazení balíčky pro architekturu ASP.NET MVC a jeho použití v tomto poli je určená jenom jako příklad, nikoli jako doporučení k němu přes jiné možnosti.

### <a name="install-the-pagedlistmvc-nuget-package"></a>Nainstalujte balíček PagedList.MVC NuGet

NuGet **PagedList.Mvc** balíček automaticky nainstaluje **PagedList** balíčku jako závislost. **PagedList** balíček nainstaluje `PagedList` kolekce typů a rozšíření metod pro `IQueryable` a `IEnumerable` kolekce. Rozšiřující metody vytvořit jednu stránku dat v `PagedList` kolekce z vaší `IQueryable` nebo `IEnumerable`a `PagedList` kolekce poskytuje několik vlastností a metod, které usnadňují stránkování. **PagedList.Mvc** balíček nainstaluje stránkování pomocné rutiny, která zobrazuje tlačítka stránkování.

1. Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** a potom **Konzola správce balíčků**.

2. V **Konzola správce balíčků** okno, ujistěte se, že **zdroj balíčku** je **nuget.org** a **výchozí projekt** je **ContosoUniversity**a potom zadejte následující příkaz:

   ```text
   Install-Package PagedList.Mvc
   ```

3. Sestavte projekt.

### <a name="add-paging-functionality-to-the-index-method"></a>Přidání funkce stránkování Index – metoda

1. V *Controllers\StudentController.cs*, přidejte `using` příkaz pro `PagedList` obor názvů:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

2. Nahradit `Index` metodu s následujícím kódem:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=1,3,7-16,41-43)]

   Tento kód přidá `page` parametr, aktuální parametr pořadí řazení a aktuální parametr filtru do podpisu metody:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

   První stránka se zobrazí, nebo pokud uživatel nebyl kliknul stránkování a řazení odkaz, všechny parametry mají hodnotu null. Pokud dojde ke kliknutí na odkaz stránkování, `page` proměnná obsahuje číslo stránky k zobrazení.

   A `ViewBag` vlastnost poskytuje zobrazení s aktuální pořadí řazení, protože to musí obsahovat odkazy stránkování aby bylo možné zachovat pořadí řazení, při stránkování stejné:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

   Jiné vlastnosti `ViewBag.CurrentFilter`, poskytuje zobrazení aktuálního řetězce filtru. Tato hodnota musí být součástí odkazy stránkování, aby byla zachována nastavení filtru během stránkování a je nutné do textového pole. až se obnoví na stránce se zobrazí znovu. Pokud hledaný řetězec se změní při stránkování, stránky se musí resetovat na hodnotu 1, protože nový filtr může vést k zobrazení různých datových. Hledaný řetězec se změní, pokud je zadána hodnota v textovém poli a stisknutí tlačítka Odeslat. V takovém případě `searchString` parametr není null.

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

   Na konci metody `ToPagedList` rozšiřující metody na studenty `IQueryable` objekt převede student dotaz na jednu stránku studentů v typu kolekce, který podporuje stránkování. Této stránce studentů je pak předán zobrazení:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

   `ToPagedList` Metoda má číslo stránky. Představují dvě otazníky [operátoru nulového sjednocení](/dotnet/csharp/language-reference/operators/null-coalescing-operator). Definuje výchozí hodnotu pro typ s možnou hodnotou Null; operátoru nulového sjednocení výraz `(page ?? 1)` znamená, že návratová hodnota z `page` Pokud má hodnotu, nebo vrátí 1, pokud `page` má hodnotu null.

### <a name="add-paging-links-to-the-student-index-view"></a>Přidat odkazy stránkování na zobrazení indexu studenta

1. V *Views\Student\Index.cshtml*, nahraďte existující kód následujícím kódem. Změny jsou zvýrazněné.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=1-3,6,9,14,17,24,30,55-56,58-59)]

   `@model` Příkazu v horní části stránky určuje, že nyní získá zobrazení `PagedList` místo objektu `List` objektu.

   `using` Příkaz pro `PagedList.Mvc` uděluje přístup do pomocné rutiny MVC pro tlačítka stránkování.

   Kód používá přetížení [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) , který umožňuje určit [FormMethod.Get](/previous-versions/aspnet/dd460179(v=vs.100)).

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cshtml?highlight=1)]

   Výchozí hodnota [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) odešle formulář dat příspěvku, což znamená, že parametry jsou předány v textu zprávy HTTP a ne v adrese URL jako řetězce dotazu. Při zadávání HTTP GET data formuláře je předána v adrese URL jako řetězce dotazu, který uživatelům umožňuje adresa URL záložky. [W3C pokyny pro použití HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) GET by měl používat, když akce nemá za následek aktualizaci.

   Do textového pole je inicializována s aktuálního vyhledávaného řetězce, takže po kliknutí na nové stránce můžete zobrazit aktuálního vyhledávaného řetězce.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml?highlight=1)]

   Odkazy záhlaví sloupce použijte řetězec dotazu k předání aktuálního vyhledávaného řetězce kontroleru tak, aby uživatel mohl třídit v rámci filtr výsledků:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1)]

   Jsou zobrazeny aktuální stránce a celkovém počtu stránek.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]

   Pokud nejsou žádné stránky pro zobrazení, se zobrazí "Stránka 0 0". (V takovém případě je větší než počet stránek číslo stránky protože `Model.PageNumber` 1, a `Model.PageCount` je 0.)

   Zobrazí se tlačítka stránkování podle `PagedListPager` pomocné rutiny:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]

   `PagedListPager` Pomocník poskytuje řadu možností, které můžete přizpůsobit, včetně adresy URL a práce se styly. Další informace najdete v tématu [TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) na webu GitHub.

2. Spuštění stránky.

   Kliknutím na odkazy stránkování v jiné pořadí řazení pro Ujistěte se, že funguje stránkování. Potom zadejte hledaný řetězec a zkuste to znovu a ověřte, že stránkování také funguje správně s řazením a filtrováním stránkování.

## <a name="create-an-about-page"></a>Vytvoření stránky o

Pro společnosti Contoso University webu o stránku budete zobrazovat, kolik studenty zaregistrovali pro každé datum registrace. To vyžaduje seskupování a jednoduché výpočtů na skupinách. K tomu budete postupujte takto:

- Vytvořte třídu modelu zobrazení dat, která je potřeba předat do zobrazení.
- Upravit `About` metodu `Home` kontroleru.
- Upravit `About` zobrazení.

### <a name="create-the-view-model"></a>Vytvoření zobrazení modelu

Vytvoření *modely ViewModels* složku ve složce projektu. V této složce, přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

### <a name="modify-the-home-controller"></a>Upravit domovského Kontrolér

1. V *HomeController.cs*, přidejte následující `using` příkazů v horní části souboru:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

2. Přidejte proměnnou třídy kontextu databáze ihned po otevření složené závorky pro třídu:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs?highlight=3)]

3. Nahradit `About` metodu s následujícím kódem:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs)]

   Příkaz LINQ skupiny studentů entity podle data registrace, vypočítá počet entit v každé skupině a ukládá výsledky v kolekci `EnrollmentDateGroup` zobrazit objekty modelu.

4. Přidat `Dispose` metody:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

### <a name="modify-the-about-view"></a>Změnit zobrazení

1. Nahraďte kód v *Views\Home\About.cshtml* souboru následujícím kódem:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml)]

2. Spusťte aplikaci a klikněte na tlačítko **o** odkaz.

   Počet studentů pro každé datum registrace se zobrazí v tabulce.

   ![About_page](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

## <a name="get-the-code"></a>Získat kód

[Stáhnout dokončený projekt](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Další zdroje

Odkazy na další zdroje Entity Framework lze nalézt v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat sloupec řazení odkazy
> * Přidání vyhledávacího pole
> * Přidání stránkování
> * Vytvoření stránky o

Přejděte k dalším článku se dozvíte, jak používat připojení odolnost proti chybám a příkaz zachycení.
> [!div class="nextstepaction"]
> [Zachycení odolnost proti chybám a příkaz připojení](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)