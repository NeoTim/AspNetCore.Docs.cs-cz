---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
title: Řazení, filtrování a stránkování s Entity Framework v aplikaci ASP.NET MVC | Dokumentace Microsoftu
author: tdykstra
description: Contoso University ukázkovou webovou aplikaci ukazuje, jak vytvářet aplikace ASP.NET MVC 5 pomocí sady Visual Studio a Entity Framework 6 Code First...
ms.author: riande
ms.date: 10/08/2018
ms.assetid: d5723e46-41fe-4d09-850a-e03b9e285bfa
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9fabb5a90af715d4e96ff79b43bfff5a4600ac08
ms.sourcegitcommit: a4dcca4f1cb81227c5ed3c92dc0e28be6e99447b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48912771"
---
# <a name="sorting-filtering-and-paging-with-the-entity-framework-in-an-aspnet-mvc-application"></a><span data-ttu-id="f3c91-103">Řazení, filtrování a stránkování s Entity Framework v aplikaci ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="f3c91-103">Sorting, filtering, and paging with the Entity Framework in an ASP.NET MVC application</span></span>

<span data-ttu-id="f3c91-104">podle [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f3c91-104">by [Tom Dykstra](https://github.com/tdykstra)</span></span>

[<span data-ttu-id="f3c91-105">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="f3c91-105">Download Completed Project</span></span>](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

> <span data-ttu-id="f3c91-106">Ukázková webová aplikace Contoso University ukazuje, jak vytvářet aplikace ASP.NET MVC 5 pomocí Entity Framework 6 kód první a Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f3c91-106">The Contoso University sample web application demonstrates how to create ASP.NET MVC 5 applications using the Entity Framework 6 Code First and Visual Studio.</span></span> <span data-ttu-id="f3c91-107">Informace o této sérii kurzů, naleznete v tématu [z prvního kurzu této série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="f3c91-107">For information about the tutorial series, see [the first tutorial in the series](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>

<span data-ttu-id="f3c91-108">V předchozím kurzu jste implementovali sadu webových stránek pro základní operace CRUD pro `Student` entity.</span><span class="sxs-lookup"><span data-stu-id="f3c91-108">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for `Student` entities.</span></span> <span data-ttu-id="f3c91-109">V tomto kurzu přidáte řazení, filtrování a stránkování funkce, které **studenty** indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f3c91-109">In this tutorial you'll add sorting, filtering, and paging functionality to the **Students** Index page.</span></span> <span data-ttu-id="f3c91-110">Také vytvoříte stránky, která provádí jednoduché seskupení.</span><span class="sxs-lookup"><span data-stu-id="f3c91-110">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="f3c91-111">Následující obrázek znázorňuje, co bude stránka vypadat až to budete mít.</span><span class="sxs-lookup"><span data-stu-id="f3c91-111">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="f3c91-112">Záhlaví sloupců jsou odkazy, které může uživatel kliknout, chcete-li seřadit podle sloupce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-112">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="f3c91-113">Kliknutím na záhlaví opakovaně sloupce přepíná mezi vzestupným a sestupným řazením.</span><span class="sxs-lookup"><span data-stu-id="f3c91-113">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Students_Index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

## <a name="add-column-sort-links-to-the-students-index-page"></a><span data-ttu-id="f3c91-115">Přidat sloupec řazení odkazy na indexovou stránku studentů</span><span class="sxs-lookup"><span data-stu-id="f3c91-115">Add column sort links to the Students index page</span></span>

<span data-ttu-id="f3c91-116">K přidání řazení Student indexovou stránku, změníte `Index` metodu `Student` kontroleru a přidejte kód, který `Student` indexu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f3c91-116">To add sorting to the Student Index page, you'll change the `Index` method of the `Student` controller and add code to the `Student` Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="f3c91-117">Přidat funkci řazení pro Index – metoda</span><span class="sxs-lookup"><span data-stu-id="f3c91-117">Add sorting functionality to the Index method</span></span>

- <span data-ttu-id="f3c91-118">V *Controllers\StudentController.cs*, nahraďte `Index` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f3c91-118">In *Controllers\StudentController.cs*, replace the `Index` method with the following code:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

<span data-ttu-id="f3c91-119">Tento kód přijme `sortOrder` parametr z řetězce dotazu v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="f3c91-119">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="f3c91-120">ASP.NET MVC poskytuje hodnotu řetězce dotazu jako parametr do metody akce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-120">The query string value is provided by ASP.NET MVC as a parameter to the action method.</span></span> <span data-ttu-id="f3c91-121">Parametr je řetězec, který je buď "Name" nebo "Data", může volitelně následovat symbol podtržítka a řetězec "desc" Zadejte sestupném pořadí.</span><span class="sxs-lookup"><span data-stu-id="f3c91-121">The parameter is a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="f3c91-122">Je výchozí pořadí řazení vzestupně.</span><span class="sxs-lookup"><span data-stu-id="f3c91-122">The default sort order is ascending.</span></span>

<span data-ttu-id="f3c91-123">Při prvním vyžádání stránky indexu není žádný řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-123">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="f3c91-124">Studenty se zobrazují ve vzestupném pořadí podle `LastName`, což je výchozí podle propuštěním případ `switch` příkazu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-124">The students are displayed in ascending order by `LastName`, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="f3c91-125">Když uživatel klikne na sloupce záhlaví hypertextový odkaz, odpovídající `sortOrder` v řetězci dotazu je zadaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="f3c91-125">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="f3c91-126">Dva `ViewBag` proměnné se používají tak, aby zobrazení můžete nakonfigurovat hypertextové odkazy záhlaví sloupce s hodnotami řetězec odpovídající dotaz:</span><span class="sxs-lookup"><span data-stu-id="f3c91-126">The two `ViewBag` variables are used so that the view can configure the column heading hyperlinks with the appropriate query string values:</span></span>

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

<span data-ttu-id="f3c91-127">Jedná se o Ternární příkazy.</span><span class="sxs-lookup"><span data-stu-id="f3c91-127">These are ternary statements.</span></span> <span data-ttu-id="f3c91-128">První z nich určuje, že pokud `sortOrder` parametr má hodnotu null nebo prázdná, `ViewBag.NameSortParm` musí být nastavená na "název\_desc"; v opačném případě musí být nastavena na prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="f3c91-128">The first one specifies that if the `sortOrder` parameter is null or empty, `ViewBag.NameSortParm` should be set to "name\_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="f3c91-129">Tyto dva příkazy Povolit zobrazení nastavte sloupec, hypertextové odkazy záhlaví následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f3c91-129">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

| <span data-ttu-id="f3c91-130">Aktuální pořadí řazení</span><span class="sxs-lookup"><span data-stu-id="f3c91-130">Current sort order</span></span> | <span data-ttu-id="f3c91-131">Poslední název hypertextový odkaz</span><span class="sxs-lookup"><span data-stu-id="f3c91-131">Last Name Hyperlink</span></span> | <span data-ttu-id="f3c91-132">Datum hypertextový odkaz</span><span class="sxs-lookup"><span data-stu-id="f3c91-132">Date Hyperlink</span></span> |
| --- | --- | --- |
| <span data-ttu-id="f3c91-133">Poslední název vzestupně</span><span class="sxs-lookup"><span data-stu-id="f3c91-133">Last Name ascending</span></span> | <span data-ttu-id="f3c91-134">descending</span><span class="sxs-lookup"><span data-stu-id="f3c91-134">descending</span></span> | <span data-ttu-id="f3c91-135">ascending</span><span class="sxs-lookup"><span data-stu-id="f3c91-135">ascending</span></span> |
| <span data-ttu-id="f3c91-136">Poslední název sestupně</span><span class="sxs-lookup"><span data-stu-id="f3c91-136">Last Name descending</span></span> | <span data-ttu-id="f3c91-137">ascending</span><span class="sxs-lookup"><span data-stu-id="f3c91-137">ascending</span></span> | <span data-ttu-id="f3c91-138">ascending</span><span class="sxs-lookup"><span data-stu-id="f3c91-138">ascending</span></span> |
| <span data-ttu-id="f3c91-139">Datum vzestupně</span><span class="sxs-lookup"><span data-stu-id="f3c91-139">Date ascending</span></span> | <span data-ttu-id="f3c91-140">ascending</span><span class="sxs-lookup"><span data-stu-id="f3c91-140">ascending</span></span> | <span data-ttu-id="f3c91-141">descending</span><span class="sxs-lookup"><span data-stu-id="f3c91-141">descending</span></span> |
| <span data-ttu-id="f3c91-142">Datum sestupně</span><span class="sxs-lookup"><span data-stu-id="f3c91-142">Date descending</span></span> | <span data-ttu-id="f3c91-143">ascending</span><span class="sxs-lookup"><span data-stu-id="f3c91-143">ascending</span></span> | <span data-ttu-id="f3c91-144">ascending</span><span class="sxs-lookup"><span data-stu-id="f3c91-144">ascending</span></span> |

<span data-ttu-id="f3c91-145">Metoda používá [technologii LINQ to Entities](/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities) zadat Tento sloupec seřadit podle.</span><span class="sxs-lookup"><span data-stu-id="f3c91-145">The method uses [LINQ to Entities](/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities) to specify the column to sort by.</span></span> <span data-ttu-id="f3c91-146">Kód vytvoří <xref:System.Linq.IQueryable%601> proměnné před `switch` ho v příkazu, změní `switch` příkazu a volání `ToList` za `switch` příkaz.</span><span class="sxs-lookup"><span data-stu-id="f3c91-146">The code creates an <xref:System.Linq.IQueryable%601> variable before the `switch` statement, modifies it in the `switch` statement, and calls the `ToList` method after the `switch` statement.</span></span> <span data-ttu-id="f3c91-147">Pokud při vytváření a úpravách `IQueryable` proměnné, žádný dotaz odeslán do databáze.</span><span class="sxs-lookup"><span data-stu-id="f3c91-147">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="f3c91-148">Dotaz není spuštěn, dokud je převést `IQueryable` objektu do kolekce voláním metody `ToList`.</span><span class="sxs-lookup"><span data-stu-id="f3c91-148">The query is not executed until you convert the `IQueryable` object into a collection by calling a method such as `ToList`.</span></span> <span data-ttu-id="f3c91-149">Proto tento kód výsledkem jednoho dotazu, který se spustí až `return View` příkazu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-149">Therefore, this code results in a single query that is not executed until the `return View` statement.</span></span>

<span data-ttu-id="f3c91-150">Jako alternativu k psaní různých příkazů LINQ pro každé pořadí řazení můžete dynamicky vytvořit dotaz LINQ.</span><span class="sxs-lookup"><span data-stu-id="f3c91-150">As an alternative to writing different LINQ statements for each sort order, you can dynamically create a LINQ statement.</span></span> <span data-ttu-id="f3c91-151">Informace o dynamické LINQ, naleznete v tématu [dynamické LINQ](https://go.microsoft.com/fwlink/?LinkID=323957).</span><span class="sxs-lookup"><span data-stu-id="f3c91-151">For information about dynamic LINQ, see [Dynamic LINQ](https://go.microsoft.com/fwlink/?LinkID=323957).</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="f3c91-152">Přidat záhlaví sloupce hypertextové odkazy do zobrazení indexu studenta</span><span class="sxs-lookup"><span data-stu-id="f3c91-152">Add column heading hyperlinks to the Student index view</span></span>

1. <span data-ttu-id="f3c91-153">V *Views\Student\Index.cshtml*, nahraďte `<tr>` a `<th>` prvky pro řádek záhlaví s zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="f3c91-153">In *Views\Student\Index.cshtml*, replace the `<tr>` and `<th>` elements for the heading row with the highlighted code:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=5-15)]

   <span data-ttu-id="f3c91-154">Tento kód používá informace v `ViewBag` vlastnosti, které chcete nastavit hypertextové odkazy s odpovídající dotaz hodnoty řetězce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-154">This code uses the information in the `ViewBag` properties to set up hyperlinks with the appropriate query string values.</span></span>

2. <span data-ttu-id="f3c91-155">Spuštění stránky a klikněte na tlačítko **příjmení** a **datum registrace** záhlaví sloupce. Ověřte, že řazení funguje.</span><span class="sxs-lookup"><span data-stu-id="f3c91-155">Run the page and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

   ![Students_Index_page_with_sort_hyperlinks](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

   <span data-ttu-id="f3c91-157">Po klepnutí **příjmení** záhlaví, studenti jsou zobrazena v sestupném pořadí poslední název.</span><span class="sxs-lookup"><span data-stu-id="f3c91-157">After you click the **Last Name** heading, students are displayed in descending last name order.</span></span>

   ![Student index zobrazení ve webovém prohlížeči](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

## <a name="add-a-search-box-to-the-students-index-page"></a><span data-ttu-id="f3c91-159">Přidání vyhledávacího pole na indexovou stránku studentů</span><span class="sxs-lookup"><span data-stu-id="f3c91-159">Add a search box to the Students index page</span></span>

<span data-ttu-id="f3c91-160">Přidání filtrování na indexovou stránku studenty, kurzu přidáte textové pole a tlačítko pro odeslání do zobrazení a provádět odpovídající změny v `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="f3c91-160">To add filtering to the Students index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="f3c91-161">Textové pole umožňuje zadat řetězec k vyhledání křestního jména a poslední název pole.</span><span class="sxs-lookup"><span data-stu-id="f3c91-161">The text box lets you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="f3c91-162">Filtrování doplňují Index – metoda</span><span class="sxs-lookup"><span data-stu-id="f3c91-162">Add filtering functionality to the Index method</span></span>

- <span data-ttu-id="f3c91-163">V *Controllers\StudentController.cs*, nahraďte `Index` – metoda (změny se zvýrazní) následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f3c91-163">In *Controllers\StudentController.cs*, replace the `Index` method with the following code (the changes are highlighted):</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=1,7-11)]

<span data-ttu-id="f3c91-164">Kód přidá `searchString` parametr `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="f3c91-164">The code adds a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="f3c91-165">Přijetí hledání řetězcovou hodnotu z textové pole, které přidáte k zobrazení indexu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-165">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="f3c91-166">Přidá také `where` klauzuli do příkazu LINQ, který vybere pouze studenti, jejichž křestní jméno nebo příjmení vyskytuje hledaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="f3c91-166">It also adds a `where` clause to the LINQ statement that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="f3c91-167">Příkaz, který se přidá <xref:System.Linq.Queryable.Where%2A> klauzule spustí pouze v případě, že je hodnota k vyhledání.</span><span class="sxs-lookup"><span data-stu-id="f3c91-167">The statement that adds the <xref:System.Linq.Queryable.Where%2A> clause executes only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="f3c91-168">V mnoha případech můžete volání stejné metody na sadu entit Entity Framework nebo jako rozšiřující metody na kolekci v paměti.</span><span class="sxs-lookup"><span data-stu-id="f3c91-168">In many cases you can call the same method either on an Entity Framework entity set or as an extension method on an in-memory collection.</span></span> <span data-ttu-id="f3c91-169">Výsledky jsou obvykle stejné, ale v některých případech může lišit.</span><span class="sxs-lookup"><span data-stu-id="f3c91-169">The results are normally the same but in some cases may be different.</span></span>
>
> <span data-ttu-id="f3c91-170">Například rozhraní .NET Framework provádění `Contains` metoda vrátí všechny řádky, pokud předáte prázdný řetězec, ale poskytovateli rozhraní Entity Framework pro SQL Server Compact 4.0 vrátí nulový počet řádků prázdné řetězce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-170">For example, the .NET Framework implementation of the `Contains` method returns all rows when you pass an empty string to it, but the Entity Framework provider for SQL Server Compact 4.0 returns zero rows for empty strings.</span></span> <span data-ttu-id="f3c91-171">Proto kódem v příkladu (vložení `Where` výroku uvnitř `if` příkaz) zajišťuje, že získáte stejné výsledky pro všechny verze SQL serveru.</span><span class="sxs-lookup"><span data-stu-id="f3c91-171">Therefore the code in the example (putting the `Where` statement inside an `if` statement) makes sure that you get the same results for all versions of SQL Server.</span></span> <span data-ttu-id="f3c91-172">Také, implementace rozhraní .NET Framework `Contains` metoda provádí porovnání velká a malá písmena ve výchozím nastavení, ale zprostředkovatele Entity Framework SQL Server provést porovnávání ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="f3c91-172">Also, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but Entity Framework SQL Server providers perform case-insensitive comparisons by default.</span></span> <span data-ttu-id="f3c91-173">Proto volání `ToUpper` metoda provést test explicitně velkých a malých písmen zajišťuje, že výsledky neměňte při změně kódu později použít úložiště, které vrátí `IEnumerable` kolekce místo `IQueryable` objektu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-173">Therefore, calling the `ToUpper` method to make the test explicitly case-insensitive ensures that results do not change when you change the code later to use a repository, which will return an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="f3c91-174">(Při volání `Contains` metodu `IEnumerable` kolekce, získat implementace rozhraní .NET Framework;. při jeho volání na `IQueryable` objektu, získáte implementace poskytovatele databáze.)</span><span class="sxs-lookup"><span data-stu-id="f3c91-174">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.)</span></span>
>
> <span data-ttu-id="f3c91-175">Null zpracování může být také jiný poskytovatelé různých databází nebo při použití `IQueryable` objektu ve srovnání s při použití `IEnumerable` kolekce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-175">Null handling may also be different for different database providers or when you use an `IQueryable` object compared to when you use an `IEnumerable` collection.</span></span> <span data-ttu-id="f3c91-176">Například v některých scénářích `Where` podmínka vyhodnocena jako `table.Column != 0` nemusí vrátit sloupce, které obsahují `null` jako hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-176">For example, in some scenarios a `Where` condition such as `table.Column != 0` may not return columns that have `null` as the value.</span></span> <span data-ttu-id="f3c91-177">Další informace najdete v tématu [nesprávné zpracování null proměnné v klauzuli where"](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl).</span><span class="sxs-lookup"><span data-stu-id="f3c91-177">For more information, see [Incorrect handling of null variables in 'where' clause](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl).</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="f3c91-178">Přidat vyhledávací pole k zobrazení indexu studenta</span><span class="sxs-lookup"><span data-stu-id="f3c91-178">Add a search box to the Student index view</span></span>

1. <span data-ttu-id="f3c91-179">V *Views\Student\Index.cshtml*, přidejte zvýrazněný kód bezprostředně před zahájením `table` tag, chcete-li vytvořit popisek, textové pole a **hledání** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="f3c91-179">In *Views\Student\Index.cshtml*, add the highlighted code immediately before the opening `table` tag in order to create a caption, a text box, and a **Search** button.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=4-11)]

2. <span data-ttu-id="f3c91-180">Spuštění stránky, zadejte vyhledávací řetězec a klikněte na tlačítko **hledání** k ověření, že filtrování funguje.</span><span class="sxs-lookup"><span data-stu-id="f3c91-180">Run the page, enter a search string, and click **Search** to verify that filtering is working.</span></span>

   ![Students_Index_page_with_search_box](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

   <span data-ttu-id="f3c91-182">Všimněte si, že adresa URL neobsahuje "k" hledaný řetězec, což znamená, že pokud označit tuto stránku záložkou, nezískáte filtrovaný seznam při použití na záložku.</span><span class="sxs-lookup"><span data-stu-id="f3c91-182">Notice the URL doesn't contain the "an" search string, which means that if you bookmark this page, you won't get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="f3c91-183">To platí také pro sloupec řazení odkazy, jak se budou řadit celý seznam.</span><span class="sxs-lookup"><span data-stu-id="f3c91-183">This applies also to the column sort links, as they will sort the whole list.</span></span> <span data-ttu-id="f3c91-184">Změníte **hledání** tlačítka pomocí řetězců dotazu pro kritéria filtru v pozdější části kurzu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-184">You'll change the **Search** button to use query strings for filter criteria later in the tutorial.</span></span>

## <a name="add-paging-to-the-students-index-page"></a><span data-ttu-id="f3c91-185">Přidání stránkování na indexovou stránku studentů</span><span class="sxs-lookup"><span data-stu-id="f3c91-185">Add paging to the Students index page</span></span>

<span data-ttu-id="f3c91-186">Přidání stránkování na indexovou stránku studenty, začnete pomocí instalace **PagedList.Mvc** balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="f3c91-186">To add paging to the Students index page, you'll start by installing the **PagedList.Mvc** NuGet package.</span></span> <span data-ttu-id="f3c91-187">Pak provede další změny v `Index` metoda a přidejte odkazy stránkování na `Index` zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f3c91-187">Then you'll make additional changes in the `Index` method and add paging links to the `Index` view.</span></span> <span data-ttu-id="f3c91-188">**PagedList.Mvc** je jednou z mnoha dobré stránkování a řazení balíčky pro architekturu ASP.NET MVC a jeho použití v tomto poli je určená jenom jako příklad, nikoli jako doporučení k němu přes jiné možnosti.</span><span class="sxs-lookup"><span data-stu-id="f3c91-188">**PagedList.Mvc** is one of many good paging and sorting packages for ASP.NET MVC, and its use here is intended only as an example, not as a recommendation for it over other options.</span></span> <span data-ttu-id="f3c91-189">Následující obrázek znázorňuje odkazy stránkování.</span><span class="sxs-lookup"><span data-stu-id="f3c91-189">The following illustration shows the paging links.</span></span>

![Students_index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

### <a name="install-the-pagedlistmvc-nuget-package"></a><span data-ttu-id="f3c91-191">Nainstalujte balíček PagedList.MVC NuGet</span><span class="sxs-lookup"><span data-stu-id="f3c91-191">Install the PagedList.MVC NuGet package</span></span>

<span data-ttu-id="f3c91-192">NuGet **PagedList.Mvc** balíček automaticky nainstaluje **PagedList** balíčku jako závislost.</span><span class="sxs-lookup"><span data-stu-id="f3c91-192">The NuGet **PagedList.Mvc** package automatically installs the **PagedList** package as a dependency.</span></span> <span data-ttu-id="f3c91-193">**PagedList** balíček nainstaluje `PagedList` kolekce typů a rozšíření metod pro `IQueryable` a `IEnumerable` kolekce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-193">The **PagedList** package installs a `PagedList` collection type and extension methods for `IQueryable` and `IEnumerable` collections.</span></span> <span data-ttu-id="f3c91-194">Rozšiřující metody vytvořit jednu stránku dat v `PagedList` kolekce z vaší `IQueryable` nebo `IEnumerable`a `PagedList` kolekce poskytuje několik vlastností a metod, které usnadňují stránkování.</span><span class="sxs-lookup"><span data-stu-id="f3c91-194">The extension methods create a single page of data in a `PagedList` collection out of your `IQueryable` or `IEnumerable`, and the `PagedList` collection provides several properties and methods that facilitate paging.</span></span> <span data-ttu-id="f3c91-195">**PagedList.Mvc** balíček nainstaluje stránkování pomocné rutiny, která zobrazuje tlačítka stránkování.</span><span class="sxs-lookup"><span data-stu-id="f3c91-195">The **PagedList.Mvc** package installs a paging helper that displays the paging buttons.</span></span>

1. <span data-ttu-id="f3c91-196">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** a potom **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="f3c91-196">From the **Tools** menu, select **NuGet Package Manager** and then **Package Manager Console**.</span></span>

2. <span data-ttu-id="f3c91-197">V **Konzola správce balíčků** okno, ujistěte se, že **zdroj balíčku** je **nuget.org** a **výchozí projekt** je **ContosoUniversity**a potom zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f3c91-197">In the **Package Manager Console** window, make sure the **Package source** is **nuget.org** and the **Default project** is **ContosoUniversity**, and then enter the following command:</span></span>

   ```text
   Install-Package PagedList.Mvc
   ```

   ![Nainstalujte PagedList.Mvc](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

3. <span data-ttu-id="f3c91-199">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="f3c91-199">Build the project.</span></span>

### <a name="add-paging-functionality-to-the-index-method"></a><span data-ttu-id="f3c91-200">Přidání funkce stránkování Index – metoda</span><span class="sxs-lookup"><span data-stu-id="f3c91-200">Add paging functionality to the Index method</span></span>

1. <span data-ttu-id="f3c91-201">V *Controllers\StudentController.cs*, přidejte `using` příkaz pro `PagedList` obor názvů:</span><span class="sxs-lookup"><span data-stu-id="f3c91-201">In *Controllers\StudentController.cs*, add a `using` statement for the `PagedList` namespace:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

2. <span data-ttu-id="f3c91-202">Nahradit `Index` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f3c91-202">Replace the `Index` method with the following code:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=1,3,7-16,41-43)]

   <span data-ttu-id="f3c91-203">Tento kód přidá `page` parametr, aktuální parametr pořadí řazení a aktuální parametr filtru do podpisu metody:</span><span class="sxs-lookup"><span data-stu-id="f3c91-203">This code adds a `page` parameter, a current sort order parameter, and a current filter parameter to the method signature:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

   <span data-ttu-id="f3c91-204">První stránka se zobrazí, nebo pokud uživatel nebyl kliknul stránkování a řazení odkaz, všechny parametry mají hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f3c91-204">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters are null.</span></span> <span data-ttu-id="f3c91-205">Pokud dojde ke kliknutí na odkaz stránkování, `page` proměnná obsahuje číslo stránky k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f3c91-205">If a paging link is clicked, the `page` variable contains the page number to display.</span></span>

   <span data-ttu-id="f3c91-206">A `ViewBag` vlastnost poskytuje zobrazení s aktuální pořadí řazení, protože to musí obsahovat odkazy stránkování aby bylo možné zachovat pořadí řazení, při stránkování stejné:</span><span class="sxs-lookup"><span data-stu-id="f3c91-206">A `ViewBag` property provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

   <span data-ttu-id="f3c91-207">Jiné vlastnosti `ViewBag.CurrentFilter`, poskytuje zobrazení aktuálního řetězce filtru.</span><span class="sxs-lookup"><span data-stu-id="f3c91-207">Another property, `ViewBag.CurrentFilter`, provides the view with the current filter string.</span></span> <span data-ttu-id="f3c91-208">Tato hodnota musí být součástí odkazy stránkování, aby byla zachována nastavení filtru během stránkování a je nutné do textového pole. až se obnoví na stránce se zobrazí znovu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-208">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span> <span data-ttu-id="f3c91-209">Pokud hledaný řetězec se změní při stránkování, stránky se musí resetovat na hodnotu 1, protože nový filtr může vést k zobrazení různých datových.</span><span class="sxs-lookup"><span data-stu-id="f3c91-209">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="f3c91-210">Hledaný řetězec se změní, pokud je zadána hodnota v textovém poli a stisknutí tlačítka Odeslat.</span><span class="sxs-lookup"><span data-stu-id="f3c91-210">The search string is changed when a value is entered in the text box and the submit button is pressed.</span></span> <span data-ttu-id="f3c91-211">V takovém případě `searchString` parametr není null.</span><span class="sxs-lookup"><span data-stu-id="f3c91-211">In that case, the `searchString` parameter is not null.</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

   <span data-ttu-id="f3c91-212">Na konci metody `ToPagedList` rozšiřující metody na studenty `IQueryable` objekt převede student dotaz na jednu stránku studentů v typu kolekce, který podporuje stránkování.</span><span class="sxs-lookup"><span data-stu-id="f3c91-212">At the end of the method, the `ToPagedList` extension method on the students `IQueryable` object converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="f3c91-213">Této stránce studentů je pak předán zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f3c91-213">That single page of students is then passed to the view:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

   <span data-ttu-id="f3c91-214">`ToPagedList` Metoda má číslo stránky.</span><span class="sxs-lookup"><span data-stu-id="f3c91-214">The `ToPagedList` method takes a page number.</span></span> <span data-ttu-id="f3c91-215">Představují dvě otazníky [operátoru nulového sjednocení](/dotnet/csharp/language-reference/operators/null-coalescing-operator).</span><span class="sxs-lookup"><span data-stu-id="f3c91-215">The two question marks represent the [null-coalescing operator](/dotnet/csharp/language-reference/operators/null-coalescing-operator).</span></span> <span data-ttu-id="f3c91-216">Definuje výchozí hodnotu pro typ s možnou hodnotou Null; operátoru nulového sjednocení výraz `(page ?? 1)` znamená, že návratová hodnota z `page` Pokud má hodnotu, nebo vrátí 1, pokud `page` má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f3c91-216">The null-coalescing operator defines a default value for a nullable type; the expression `(page ?? 1)` means return the value of `page` if it has a value, or return 1 if `page` is null.</span></span>

### <a name="add-paging-links-to-the-student-index-view"></a><span data-ttu-id="f3c91-217">Přidat odkazy stránkování na zobrazení indexu studenta</span><span class="sxs-lookup"><span data-stu-id="f3c91-217">Add paging links to the Student index view</span></span>

1. <span data-ttu-id="f3c91-218">V *Views\Student\Index.cshtml*, nahraďte existující kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="f3c91-218">In *Views\Student\Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="f3c91-219">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="f3c91-219">The changes are highlighted.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=1-3,6,9,14,17,24,30,55-56,58-59)]

   <span data-ttu-id="f3c91-220">`@model` Příkazu v horní části stránky určuje, že nyní získá zobrazení `PagedList` místo objektu `List` objektu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-220">The `@model` statement at the top of the page specifies that the view now gets a `PagedList` object instead of a `List` object.</span></span>

   <span data-ttu-id="f3c91-221">`using` Příkaz pro `PagedList.Mvc` uděluje přístup do pomocné rutiny MVC pro tlačítka stránkování.</span><span class="sxs-lookup"><span data-stu-id="f3c91-221">The `using` statement for `PagedList.Mvc` gives access to the MVC helper for the paging buttons.</span></span>

   <span data-ttu-id="f3c91-222">Kód používá přetížení [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) , který umožňuje určit [FormMethod.Get](/previous-versions/aspnet/dd460179(v=vs.100)).</span><span class="sxs-lookup"><span data-stu-id="f3c91-222">The code uses an overload of [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) that allows it to specify [FormMethod.Get](/previous-versions/aspnet/dd460179(v=vs.100)).</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cshtml?highlight=1)]

   <span data-ttu-id="f3c91-223">Výchozí hodnota [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) odešle formulář dat příspěvku, což znamená, že parametry jsou předány v textu zprávy HTTP a ne v adrese URL jako řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-223">The default [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="f3c91-224">Při zadávání HTTP GET data formuláře je předána v adrese URL jako řetězce dotazu, který uživatelům umožňuje adresa URL záložky.</span><span class="sxs-lookup"><span data-stu-id="f3c91-224">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="f3c91-225">[W3C pokyny pro použití HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) GET by měl používat, když akce nemá za následek aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="f3c91-225">The [W3C guidelines for the use of HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) recommend that you should use GET when the action does not result in an update.</span></span>

   <span data-ttu-id="f3c91-226">Do textového pole je inicializována s aktuálního vyhledávaného řetězce, takže po kliknutí na nové stránce můžete zobrazit aktuálního vyhledávaného řetězce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-226">The text box is initialized with the current search string so when you click a new page you can see the current search string.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml?highlight=1)]

   <span data-ttu-id="f3c91-227">Odkazy záhlaví sloupce použijte řetězec dotazu k předání aktuálního vyhledávaného řetězce kontroleru tak, aby uživatel mohl třídit v rámci filtr výsledků:</span><span class="sxs-lookup"><span data-stu-id="f3c91-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1)]

   <span data-ttu-id="f3c91-228">Jsou zobrazeny aktuální stránce a celkovém počtu stránek.</span><span class="sxs-lookup"><span data-stu-id="f3c91-228">The current page and total number of pages are displayed.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]

   <span data-ttu-id="f3c91-229">Pokud nejsou žádné stránky pro zobrazení, se zobrazí "Stránka 0 0".</span><span class="sxs-lookup"><span data-stu-id="f3c91-229">If there are no pages to display, "Page 0 of 0" is shown.</span></span> <span data-ttu-id="f3c91-230">(V takovém případě je větší než počet stránek číslo stránky protože `Model.PageNumber` 1, a `Model.PageCount` je 0.)</span><span class="sxs-lookup"><span data-stu-id="f3c91-230">(In that case the page number is greater than the page count because `Model.PageNumber` is 1, and `Model.PageCount` is 0.)</span></span>

   <span data-ttu-id="f3c91-231">Zobrazí se tlačítka stránkování podle `PagedListPager` pomocné rutiny:</span><span class="sxs-lookup"><span data-stu-id="f3c91-231">The paging buttons are displayed by the `PagedListPager` helper:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]

   <span data-ttu-id="f3c91-232">`PagedListPager` Pomocník poskytuje řadu možností, které můžete přizpůsobit, včetně adresy URL a práce se styly.</span><span class="sxs-lookup"><span data-stu-id="f3c91-232">The `PagedListPager` helper provides a number of options that you can customize, including URLs and styling.</span></span> <span data-ttu-id="f3c91-233">Další informace najdete v tématu [TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) na webu GitHub.</span><span class="sxs-lookup"><span data-stu-id="f3c91-233">For more information, see [TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) on the GitHub site.</span></span>

2. <span data-ttu-id="f3c91-234">Spuštění stránky.</span><span class="sxs-lookup"><span data-stu-id="f3c91-234">Run the page.</span></span>

   ![Studenti indexovou stránku s stránkování](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

   <span data-ttu-id="f3c91-236">Kliknutím na odkazy stránkování v jiné pořadí řazení pro Ujistěte se, že funguje stránkování.</span><span class="sxs-lookup"><span data-stu-id="f3c91-236">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="f3c91-237">Potom zadejte hledaný řetězec a zkuste to znovu a ověřte, že stránkování také funguje správně s řazením a filtrováním stránkování.</span><span class="sxs-lookup"><span data-stu-id="f3c91-237">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

   ![Studenti index stránky s filtrovaným textem vyhledávání](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

## <a name="create-an-about-page-that-shows-student-statistics"></a><span data-ttu-id="f3c91-239">Vytvořte o stránku, která zobrazuje statistiku studenta</span><span class="sxs-lookup"><span data-stu-id="f3c91-239">Create an About page that shows Student statistics</span></span>

<span data-ttu-id="f3c91-240">Pro společnosti Contoso University webu o stránku budete zobrazovat, kolik studenty zaregistrovali pro každé datum registrace.</span><span class="sxs-lookup"><span data-stu-id="f3c91-240">For the Contoso University website's About page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="f3c91-241">To vyžaduje seskupování a jednoduché výpočtů na skupinách.</span><span class="sxs-lookup"><span data-stu-id="f3c91-241">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="f3c91-242">K tomu budete postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="f3c91-242">To accomplish this, you'll do the following:</span></span>

- <span data-ttu-id="f3c91-243">Vytvořte třídu modelu zobrazení dat, která je potřeba předat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f3c91-243">Create a view model class for the data that you need to pass to the view.</span></span>
- <span data-ttu-id="f3c91-244">Upravit `About` metodu `Home` kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f3c91-244">Modify the `About` method in the `Home` controller.</span></span>
- <span data-ttu-id="f3c91-245">Upravit `About` zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f3c91-245">Modify the `About` view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="f3c91-246">Vytvoření zobrazení modelu</span><span class="sxs-lookup"><span data-stu-id="f3c91-246">Create the View Model</span></span>

<span data-ttu-id="f3c91-247">Vytvoření *modely ViewModels* složku ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-247">Create a *ViewModels* folder in the project folder.</span></span> <span data-ttu-id="f3c91-248">V této složce, přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f3c91-248">In that folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="f3c91-249">Upravit domovského Kontrolér</span><span class="sxs-lookup"><span data-stu-id="f3c91-249">Modify the Home Controller</span></span>

1. <span data-ttu-id="f3c91-250">V *HomeController.cs*, přidejte následující `using` příkazů v horní části souboru:</span><span class="sxs-lookup"><span data-stu-id="f3c91-250">In *HomeController.cs*, add the following `using` statements at the top of the file:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

2. <span data-ttu-id="f3c91-251">Přidejte proměnnou třídy kontextu databáze ihned po otevření složené závorky pro třídu:</span><span class="sxs-lookup"><span data-stu-id="f3c91-251">Add a class variable for the database context immediately after the opening curly brace for the class:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs?highlight=3)]

3. <span data-ttu-id="f3c91-252">Nahradit `About` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f3c91-252">Replace the `About` method with the following code:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs)]

   <span data-ttu-id="f3c91-253">Příkaz LINQ skupiny studentů entity podle data registrace, vypočítá počet entit v každé skupině a ukládá výsledky v kolekci `EnrollmentDateGroup` zobrazit objekty modelu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-253">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

4. <span data-ttu-id="f3c91-254">Přidat `Dispose` metody:</span><span class="sxs-lookup"><span data-stu-id="f3c91-254">Add a `Dispose` method:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

### <a name="modify-the-about-view"></a><span data-ttu-id="f3c91-255">Změnit zobrazení</span><span class="sxs-lookup"><span data-stu-id="f3c91-255">Modify the About View</span></span>

1. <span data-ttu-id="f3c91-256">Nahraďte kód v *Views\Home\About.cshtml* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f3c91-256">Replace the code in the *Views\Home\About.cshtml* file with the following code:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml)]

2. <span data-ttu-id="f3c91-257">Spusťte aplikaci a klikněte na tlačítko **o** odkaz.</span><span class="sxs-lookup"><span data-stu-id="f3c91-257">Run the app and click the **About** link.</span></span>

   <span data-ttu-id="f3c91-258">Počet studentů pro každé datum registrace se zobrazí v tabulce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-258">The count of students for each enrollment date displays in a table.</span></span>

   ![About_page](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

## <a name="summary"></a><span data-ttu-id="f3c91-260">Souhrn</span><span class="sxs-lookup"><span data-stu-id="f3c91-260">Summary</span></span>

<span data-ttu-id="f3c91-261">V tomto kurzu jste viděli, jak vytvořit datový model a provádět základní CRUD, řazení, filtrování, stránkování a seskupování funkce.</span><span class="sxs-lookup"><span data-stu-id="f3c91-261">In this tutorial you've seen how to create a data model and implement basic CRUD, sorting, filtering, paging, and grouping functionality.</span></span> <span data-ttu-id="f3c91-262">V dalším kurzu se zobrazí za přibližně pohledu na pokročilejší témata tak, že rozbalíte datového modelu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-262">In the next tutorial, you'll begin looking at more advanced topics by expanding the data model.</span></span>

<span data-ttu-id="f3c91-263">Jak vám v tomto kurzu líbilo a co můžeme zlepšit nám prosím zpětnou vazbu.</span><span class="sxs-lookup"><span data-stu-id="f3c91-263">Please leave feedback on how you liked this tutorial and what we could improve.</span></span>

<span data-ttu-id="f3c91-264">Odkazy na další zdroje Entity Framework lze nalézt v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="f3c91-264">Links to other Entity Framework resources can be found in [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f3c91-265">[Předchozí](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)
> [další](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)</span><span class="sxs-lookup"><span data-stu-id="f3c91-265">[Previous](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)
[Next](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)</span></span>
