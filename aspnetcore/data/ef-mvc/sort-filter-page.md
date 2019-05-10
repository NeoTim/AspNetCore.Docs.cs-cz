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
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="64e78-104">Kurz: Přidat řazení, filtrování a stránkování – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="64e78-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="64e78-105">V předchozím kurzu jste implementovali sadu webových stránek pro základní operace CRUD pro studenty entity.</span><span class="sxs-lookup"><span data-stu-id="64e78-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="64e78-106">V tomto kurzu přidáte řazení, filtrování a stránkování funkce pro studenty indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="64e78-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="64e78-107">Také vytvoříte stránky, která provádí jednoduché seskupení.</span><span class="sxs-lookup"><span data-stu-id="64e78-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="64e78-108">Následující obrázek znázorňuje, co bude stránka vypadat až to budete mít.</span><span class="sxs-lookup"><span data-stu-id="64e78-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="64e78-109">Záhlaví sloupců jsou odkazy, které může uživatel kliknout, chcete-li seřadit podle sloupce.</span><span class="sxs-lookup"><span data-stu-id="64e78-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="64e78-110">Kliknutím na záhlaví opakovaně sloupce přepíná mezi vzestupným a sestupným řazením.</span><span class="sxs-lookup"><span data-stu-id="64e78-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Studenti indexová stránka](sort-filter-page/_static/paging.png)

<span data-ttu-id="64e78-112">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="64e78-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="64e78-113">Přidat sloupec řazení odkazy</span><span class="sxs-lookup"><span data-stu-id="64e78-113">Add column sort links</span></span>
> * <span data-ttu-id="64e78-114">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="64e78-114">Add a Search box</span></span>
> * <span data-ttu-id="64e78-115">Přidání stránkování k indexu studentů</span><span class="sxs-lookup"><span data-stu-id="64e78-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="64e78-116">Přidání stránkování k Index – metoda</span><span class="sxs-lookup"><span data-stu-id="64e78-116">Add paging to Index method</span></span>
> * <span data-ttu-id="64e78-117">Přidat odkazy stránkování</span><span class="sxs-lookup"><span data-stu-id="64e78-117">Add paging links</span></span>
> * <span data-ttu-id="64e78-118">Vytvoření stránky o</span><span class="sxs-lookup"><span data-stu-id="64e78-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="64e78-119">Požadavky</span><span class="sxs-lookup"><span data-stu-id="64e78-119">Prerequisites</span></span>

* [<span data-ttu-id="64e78-120">Implementace funkcí CRUD</span><span class="sxs-lookup"><span data-stu-id="64e78-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="64e78-121">Přidat sloupec řazení odkazy</span><span class="sxs-lookup"><span data-stu-id="64e78-121">Add column sort links</span></span>

<span data-ttu-id="64e78-122">K přidání řazení Student indexovou stránku, změníte `Index` metody kontroleru studenty a přidejte kód k zobrazení indexu studentů.</span><span class="sxs-lookup"><span data-stu-id="64e78-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="64e78-123">Přidání funkce řazení Index – metoda</span><span class="sxs-lookup"><span data-stu-id="64e78-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="64e78-124">V *StudentsController.cs*, nahraďte `Index` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="64e78-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="64e78-125">Tento kód přijme `sortOrder` parametr z řetězce dotazu v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="64e78-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="64e78-126">ASP.NET Core MVC poskytuje hodnotu řetězce dotazu jako parametr do metody akce.</span><span class="sxs-lookup"><span data-stu-id="64e78-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="64e78-127">Parametr bude řetězec, který je buď "Name" nebo "Data", může volitelně následovat symbol podtržítka a řetězec "desc" Zadejte sestupném pořadí.</span><span class="sxs-lookup"><span data-stu-id="64e78-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="64e78-128">Je výchozí pořadí řazení vzestupně.</span><span class="sxs-lookup"><span data-stu-id="64e78-128">The default sort order is ascending.</span></span>

<span data-ttu-id="64e78-129">Při prvním vyžádání stránky indexu není žádný řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="64e78-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="64e78-130">Studenty se zobrazují ve vzestupném pořadí podle příjmení, což je výchozí podle propuštěním případ `switch` příkazu.</span><span class="sxs-lookup"><span data-stu-id="64e78-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="64e78-131">Když uživatel klikne na sloupce záhlaví hypertextový odkaz, odpovídající `sortOrder` v řetězci dotazu je zadaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="64e78-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="64e78-132">Dvě `ViewData` prvky (NameSortParm a DateSortParm) se zobrazením používají ke konfiguraci hypertextové odkazy záhlaví sloupce s hodnotami řetězec dotazu odpovídající.</span><span class="sxs-lookup"><span data-stu-id="64e78-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="64e78-133">Jedná se o Ternární příkazy.</span><span class="sxs-lookup"><span data-stu-id="64e78-133">These are ternary statements.</span></span> <span data-ttu-id="64e78-134">První z nich určuje, že pokud `sortOrder` parametr má hodnotu null nebo prázdná, NameSortParm musí být nastavená na "name_desc"; v opačném případě by měla být nastavena na prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="64e78-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="64e78-135">Tyto dva příkazy Povolit zobrazení nastavte sloupec, hypertextové odkazy záhlaví následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="64e78-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="64e78-136">Aktuální pořadí řazení</span><span class="sxs-lookup"><span data-stu-id="64e78-136">Current sort order</span></span>  | <span data-ttu-id="64e78-137">Poslední název hypertextový odkaz</span><span class="sxs-lookup"><span data-stu-id="64e78-137">Last Name Hyperlink</span></span> | <span data-ttu-id="64e78-138">Datum hypertextový odkaz</span><span class="sxs-lookup"><span data-stu-id="64e78-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="64e78-139">Poslední název vzestupně</span><span class="sxs-lookup"><span data-stu-id="64e78-139">Last Name ascending</span></span>  | <span data-ttu-id="64e78-140">descending</span><span class="sxs-lookup"><span data-stu-id="64e78-140">descending</span></span>          | <span data-ttu-id="64e78-141">ascending</span><span class="sxs-lookup"><span data-stu-id="64e78-141">ascending</span></span>      |
| <span data-ttu-id="64e78-142">Poslední název sestupně</span><span class="sxs-lookup"><span data-stu-id="64e78-142">Last Name descending</span></span> | <span data-ttu-id="64e78-143">ascending</span><span class="sxs-lookup"><span data-stu-id="64e78-143">ascending</span></span>           | <span data-ttu-id="64e78-144">ascending</span><span class="sxs-lookup"><span data-stu-id="64e78-144">ascending</span></span>      |
| <span data-ttu-id="64e78-145">Datum vzestupně</span><span class="sxs-lookup"><span data-stu-id="64e78-145">Date ascending</span></span>       | <span data-ttu-id="64e78-146">ascending</span><span class="sxs-lookup"><span data-stu-id="64e78-146">ascending</span></span>           | <span data-ttu-id="64e78-147">descending</span><span class="sxs-lookup"><span data-stu-id="64e78-147">descending</span></span>     |
| <span data-ttu-id="64e78-148">Datum sestupně</span><span class="sxs-lookup"><span data-stu-id="64e78-148">Date descending</span></span>      | <span data-ttu-id="64e78-149">ascending</span><span class="sxs-lookup"><span data-stu-id="64e78-149">ascending</span></span>           | <span data-ttu-id="64e78-150">ascending</span><span class="sxs-lookup"><span data-stu-id="64e78-150">ascending</span></span>      |

<span data-ttu-id="64e78-151">Metoda používá k určení tento sloupec seřadit podle technologie LINQ to Entities.</span><span class="sxs-lookup"><span data-stu-id="64e78-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="64e78-152">Kód vytvoří `IQueryable` proměnnou před příkazem switch ho změní v příkazu switch a volání `ToListAsync` za `switch` příkazu.</span><span class="sxs-lookup"><span data-stu-id="64e78-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="64e78-153">Pokud při vytváření a úpravách `IQueryable` proměnné, žádný dotaz odeslán do databáze.</span><span class="sxs-lookup"><span data-stu-id="64e78-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="64e78-154">Dotaz není spuštěn, dokud je převést `IQueryable` objektu do kolekce voláním metody `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="64e78-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="64e78-155">Proto tento kód výsledkem jednoho dotazu, který se spustí až `return View` příkazu.</span><span class="sxs-lookup"><span data-stu-id="64e78-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="64e78-156">Tento kód by mohl získat podrobné s velkým počtem sloupců.</span><span class="sxs-lookup"><span data-stu-id="64e78-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="64e78-157">[V posledním kurzu této série](advanced.md#dynamic-linq) ukazuje, jak napsat kód, který umožňuje předat název `OrderBy` sloupce v proměnné typu řetězec.</span><span class="sxs-lookup"><span data-stu-id="64e78-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="64e78-158">Přidat záhlaví sloupce hypertextové odkazy do zobrazení indexu studenta</span><span class="sxs-lookup"><span data-stu-id="64e78-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="64e78-159">Nahraďte kód v *Views/Students/Index.cshtml*, následujícím kódem, jak přidat hypertextové odkazy záhlaví sloupce.</span><span class="sxs-lookup"><span data-stu-id="64e78-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="64e78-160">Změněné řádky jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="64e78-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="64e78-161">Tento kód používá informace v `ViewData` hodnoty vlastnosti, které chcete nastavit hypertextové odkazy s odpovídající dotaz řetězce.</span><span class="sxs-lookup"><span data-stu-id="64e78-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="64e78-162">Spusťte aplikaci, vyberte **studenty** kartu a klikněte na tlačítko **příjmení** a **datum registrace** záhlaví sloupce. Ověřte, že řazení funguje.</span><span class="sxs-lookup"><span data-stu-id="64e78-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Studenti indexovou stránku podle názvu](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="64e78-164">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="64e78-164">Add a Search box</span></span>

<span data-ttu-id="64e78-165">Pokud chcete přidat, filtrování, abyste studenty indexovou stránku, budete přidat textové pole a tlačítko pro odeslání do zobrazení a provádět odpovídající změny v `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="64e78-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="64e78-166">Do textového pole umožňují zadat řetězec k vyhledání křestního jména a poslední název pole.</span><span class="sxs-lookup"><span data-stu-id="64e78-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="64e78-167">Filtrování doplňují Index – metoda</span><span class="sxs-lookup"><span data-stu-id="64e78-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="64e78-168">V *StudentsController.cs*, nahraďte `Index` – metoda (změny se zvýrazní) následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="64e78-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="64e78-169">Přidání `searchString` parametr `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="64e78-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="64e78-170">Přijetí hledání řetězcovou hodnotu z textové pole, které přidáte k zobrazení indexu.</span><span class="sxs-lookup"><span data-stu-id="64e78-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="64e78-171">Jste také přidali do příkazu LINQ where klauzuli, která vybere pouze studenti, jejichž křestní jméno nebo příjmení vyskytuje hledaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="64e78-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="64e78-172">Příkaz, který přidá where – klauzule provádí pouze v případě, že je hodnota k vyhledání.</span><span class="sxs-lookup"><span data-stu-id="64e78-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="64e78-173">Tady jsou volání `Where` metodu `IQueryable` objekt a Filtr zpracuje na serveru.</span><span class="sxs-lookup"><span data-stu-id="64e78-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="64e78-174">V některých scénářích může být volání `Where` metody jako metody rozšíření v kolekci v paměti.</span><span class="sxs-lookup"><span data-stu-id="64e78-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="64e78-175">(Například Předpokládejme, že změníte odkaz na `_context.Students` tak místo z EF `DbSet` odkazuje na metodu úložiště, která se vrátí `IEnumerable` kolekce.) Výsledek by obvykle stejný, ale v některých případech může lišit.</span><span class="sxs-lookup"><span data-stu-id="64e78-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="64e78-176">Například rozhraní .NET Framework provádění `Contains` metoda provádí porovnání velká a malá písmena ve výchozím nastavení, ale v systému SQL Server se určuje podle nastavení kolace instance systému SQL Server.</span><span class="sxs-lookup"><span data-stu-id="64e78-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="64e78-177">Toto nastavení výchozí hodnoty na velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="64e78-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="64e78-178">Lze zavolat `ToUpper` metoda provést test explicitně velká a malá písmena:  *Kde (s = > s.LastName.ToUpper(). Contains(searchString.ToUpper())*.</span><span class="sxs-lookup"><span data-stu-id="64e78-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="64e78-179">Který zajistí, že výsledky zůstanou stejné, při změně kódu později použít úložiště, které vrátí `IEnumerable` kolekce místo `IQueryable` objektu.</span><span class="sxs-lookup"><span data-stu-id="64e78-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="64e78-180">(Při volání `Contains` metodu `IEnumerable` kolekce, získat implementace rozhraní .NET Framework;. při jeho volání na `IQueryable` objektu, získáte implementace poskytovatele databáze.) Je však snížení výkonu pro toto řešení.</span><span class="sxs-lookup"><span data-stu-id="64e78-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="64e78-181">`ToUpper` Vložili kódu funkce v klauzuli WHERE příkazu TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="64e78-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="64e78-182">Pomocí indexu, který by jinak znemožňovaly Optimalizátor.</span><span class="sxs-lookup"><span data-stu-id="64e78-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="64e78-183">Vzhledem k tomu, že SQL většinou nainstalovaná jako velkých a malých písmen, je nejlepší je vyhnout `ToUpper` kód, dokud nemigrujete do úložiště dat malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="64e78-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="64e78-184">Přidat vyhledávací pole k zobrazení indexu studenta</span><span class="sxs-lookup"><span data-stu-id="64e78-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="64e78-185">V *Views/Student/Index.cshtml*, přidejte zvýrazněný kód bezprostředně před zahájení Chcete-li vytvořit popisek, textové pole, značka tabulky a **hledání** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="64e78-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="64e78-186">Tento kód používá `<form>` [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) přidáte textové pole hledání a tlačítko.</span><span class="sxs-lookup"><span data-stu-id="64e78-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="64e78-187">Ve výchozím nastavení `<form>` pomocné rutiny značky odešle formulář dat příspěvku, což znamená, že parametry jsou předány v textu zprávy HTTP a ne v adrese URL jako řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="64e78-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="64e78-188">Při zadávání HTTP GET data formuláře je předána v adrese URL jako řetězce dotazu, který uživatelům umožňuje adresa URL záložky.</span><span class="sxs-lookup"><span data-stu-id="64e78-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="64e78-189">Doporučujeme pokyny W3C, abyste používali získáte akce nevede k aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="64e78-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="64e78-190">Spusťte aplikaci, vyberte **studenty** kartu, zadejte vyhledávací řetězec a kliknutím na tlačítko Hledat ověřte, že filtrování funguje.</span><span class="sxs-lookup"><span data-stu-id="64e78-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Studenti indexovou stránku s filtrováním](sort-filter-page/_static/filtering.png)

<span data-ttu-id="64e78-192">Všimněte si, že adresa URL vyskytuje hledaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="64e78-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="64e78-193">Pokud se označit tuto stránku záložkou, získáte při použití na záložku filtrovaný seznam.</span><span class="sxs-lookup"><span data-stu-id="64e78-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="64e78-194">Přidání `method="get"` k `form` značka je, co způsobilo řetězec dotazu, který nevygeneruje.</span><span class="sxs-lookup"><span data-stu-id="64e78-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="64e78-195">V této fázi, pokud kliknete na sloupce záhlaví řazení odkaz ztratíte hodnota filtru, který jste zadali v **hledání** pole.</span><span class="sxs-lookup"><span data-stu-id="64e78-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="64e78-196">Opravíte to v další části.</span><span class="sxs-lookup"><span data-stu-id="64e78-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="64e78-197">Přidání stránkování k indexu studentů</span><span class="sxs-lookup"><span data-stu-id="64e78-197">Add paging to Students Index</span></span>

<span data-ttu-id="64e78-198">Přidání stránkování k studenty indexovou stránku, vytvoříte `PaginatedList` třídu, která používá `Skip` a `Take` příkazy k filtrování dat na serveru, místo vždy načítání všech řádků v tabulce.</span><span class="sxs-lookup"><span data-stu-id="64e78-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="64e78-199">Pak provede další změny v `Index` metoda a přidat tlačítka stránkování `Index` zobrazení.</span><span class="sxs-lookup"><span data-stu-id="64e78-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="64e78-200">Následující obrázek znázorňuje tlačítka stránkování.</span><span class="sxs-lookup"><span data-stu-id="64e78-200">The following illustration shows the paging buttons.</span></span>

![Studenti index stránky s odkazy stránkování](sort-filter-page/_static/paging.png)

<span data-ttu-id="64e78-202">Ve složce projektu vytvořit `PaginatedList.cs`a potom nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="64e78-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="64e78-203">`CreateAsync` Metoda v tomto kódu má velikost stránky a číslo stránky a použije příslušné `Skip` a `Take` příkazy `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="64e78-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="64e78-204">Když `ToListAsync` je volán na `IQueryable`, vrátí seznam obsahující pouze požadované stránky.</span><span class="sxs-lookup"><span data-stu-id="64e78-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="64e78-205">Vlastnosti `HasPreviousPage` a `HasNextPage` slouží k povolení nebo zakázání **předchozí** a **Další** tlačítka stránkování.</span><span class="sxs-lookup"><span data-stu-id="64e78-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="64e78-206">A `CreateAsync` metoda se používá namísto konstruktor k vytvoření `PaginatedList<T>` objektu, protože konstruktory nelze spustit asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="64e78-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="64e78-207">Přidání stránkování k Index – metoda</span><span class="sxs-lookup"><span data-stu-id="64e78-207">Add paging to Index method</span></span>

<span data-ttu-id="64e78-208">V *StudentsController.cs*, nahraďte `Index` metodu s následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="64e78-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="64e78-209">Tento kód přidá parametr číslo stránky, aktuální parametr pořadí řazení a aktuální parametr filtru do podpisu metody.</span><span class="sxs-lookup"><span data-stu-id="64e78-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="64e78-210">První stránka se zobrazí, nebo pokud uživatel nebyl kliknul stránkování a řazení odkaz, všechny parametry budou mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="64e78-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="64e78-211">Pokud dojde ke kliknutí na odkaz stránkování, proměnnou stránky bude obsahovat číslo stránky k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="64e78-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="64e78-212">`ViewData` Element s názvem CurrentSort poskytuje zobrazení s aktuální pořadí řazení, protože to musí obsahovat odkazy stránkování aby bylo možné zachovat pořadí řazení, při stránkování stejné.</span><span class="sxs-lookup"><span data-stu-id="64e78-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="64e78-213">`ViewData` Element s názvem CurrentFilter poskytuje zobrazení s aktuální řetězec filtru.</span><span class="sxs-lookup"><span data-stu-id="64e78-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="64e78-214">Tato hodnota musí být součástí odkazy stránkování, aby byla zachována nastavení filtru během stránkování a je nutné do textového pole. až se obnoví na stránce se zobrazí znovu.</span><span class="sxs-lookup"><span data-stu-id="64e78-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="64e78-215">Pokud hledaný řetězec se změní při stránkování, stránky se musí resetovat na hodnotu 1, protože nový filtr může vést k zobrazení různých datových.</span><span class="sxs-lookup"><span data-stu-id="64e78-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="64e78-216">Hledaný řetězec se změní, pokud je zadána hodnota v textovém poli a stisknutí tlačítka Odeslat.</span><span class="sxs-lookup"><span data-stu-id="64e78-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="64e78-217">V takovém případě `searchString` parametr není null.</span><span class="sxs-lookup"><span data-stu-id="64e78-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="64e78-218">Na konci `Index` metody, `PaginatedList.CreateAsync` metoda převede student dotazu na jednu stránku studentů v typu kolekce, který podporuje stránkování.</span><span class="sxs-lookup"><span data-stu-id="64e78-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="64e78-219">Této stránce studentů je pak předán zobrazení.</span><span class="sxs-lookup"><span data-stu-id="64e78-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="64e78-220">`PaginatedList.CreateAsync` Metoda má číslo stránky.</span><span class="sxs-lookup"><span data-stu-id="64e78-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="64e78-221">Dvě otazníky představují operátoru nulového sjednocení.</span><span class="sxs-lookup"><span data-stu-id="64e78-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="64e78-222">Definuje výchozí hodnotu pro typ s možnou hodnotou Null; operátoru nulového sjednocení výraz `(pageNumber ?? 1)` znamená, že návratová hodnota z `pageNumber` Pokud má hodnotu, nebo vrátí 1, pokud `pageNumber` má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="64e78-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="64e78-223">Přidat odkazy stránkování</span><span class="sxs-lookup"><span data-stu-id="64e78-223">Add paging links</span></span>

<span data-ttu-id="64e78-224">V *Views/Students/Index.cshtml*, nahraďte existující kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="64e78-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="64e78-225">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="64e78-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="64e78-226">`@model` Příkazu v horní části stránky určuje, že nyní získá zobrazení `PaginatedList<T>` místo objektu `List<T>` objektu.</span><span class="sxs-lookup"><span data-stu-id="64e78-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="64e78-227">Odkazy záhlaví sloupce použijte řetězec dotazu k předání aktuálního vyhledávaného řetězce kontroleru tak, aby uživatel mohl třídit v rámci filtr výsledků:</span><span class="sxs-lookup"><span data-stu-id="64e78-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="64e78-228">Mají tlačítka stránkování podle pomocných rutin značek:</span><span class="sxs-lookup"><span data-stu-id="64e78-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="64e78-229">Spusťte aplikaci a přejděte na stránku pro studenty.</span><span class="sxs-lookup"><span data-stu-id="64e78-229">Run the app and go to the Students page.</span></span>

![Studenti index stránky s odkazy stránkování](sort-filter-page/_static/paging.png)

<span data-ttu-id="64e78-231">Kliknutím na odkazy stránkování v jiné pořadí řazení pro Ujistěte se, že funguje stránkování.</span><span class="sxs-lookup"><span data-stu-id="64e78-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="64e78-232">Potom zadejte hledaný řetězec a zkuste to znovu a ověřte, že stránkování také funguje správně s řazením a filtrováním stránkování.</span><span class="sxs-lookup"><span data-stu-id="64e78-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="64e78-233">Vytvoření stránky o</span><span class="sxs-lookup"><span data-stu-id="64e78-233">Create an About page</span></span>

<span data-ttu-id="64e78-234">Pro web společnosti Contoso University **o** stránce bude zobrazovat, kolik studenty zaregistrovali pro každé datum registrace.</span><span class="sxs-lookup"><span data-stu-id="64e78-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="64e78-235">To vyžaduje seskupování a jednoduché výpočtů na skupinách.</span><span class="sxs-lookup"><span data-stu-id="64e78-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="64e78-236">K tomu budete postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="64e78-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="64e78-237">Vytvořte třídu modelu zobrazení dat, která je potřeba předat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="64e78-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="64e78-238">Vytvořte metodu o v kontroler Home.</span><span class="sxs-lookup"><span data-stu-id="64e78-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="64e78-239">Vytvořte zobrazení o.</span><span class="sxs-lookup"><span data-stu-id="64e78-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="64e78-240">Vytvoření zobrazení modelu</span><span class="sxs-lookup"><span data-stu-id="64e78-240">Create the view model</span></span>

<span data-ttu-id="64e78-241">Vytvoření *SchoolViewModels* složky *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="64e78-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="64e78-242">Nové složky, přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="64e78-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="64e78-243">Upravit domovského Kontrolér</span><span class="sxs-lookup"><span data-stu-id="64e78-243">Modify the Home Controller</span></span>

<span data-ttu-id="64e78-244">V *HomeController.cs*, přidejte následující příkazy using v horní části souboru:</span><span class="sxs-lookup"><span data-stu-id="64e78-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="64e78-245">Přidat proměnnou třídy kontextu databáze ihned po otevření složené závorky pro třídu a získání instance kontextu z ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="64e78-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="64e78-246">Přidat `About` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="64e78-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="64e78-247">Příkaz LINQ skupiny studentů entity podle data registrace, vypočítá počet entit v každé skupině a ukládá výsledky v kolekci `EnrollmentDateGroup` zobrazit objekty modelu.</span><span class="sxs-lookup"><span data-stu-id="64e78-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="64e78-248">Vytvořit zobrazení</span><span class="sxs-lookup"><span data-stu-id="64e78-248">Create the About View</span></span>

<span data-ttu-id="64e78-249">Přidat *Views/Home/About.cshtml* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="64e78-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="64e78-250">Spusťte aplikaci a přejděte na stránku o.</span><span class="sxs-lookup"><span data-stu-id="64e78-250">Run the app and go to the About page.</span></span> <span data-ttu-id="64e78-251">Počet studentů pro každé datum registrace se zobrazí v tabulce.</span><span class="sxs-lookup"><span data-stu-id="64e78-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="64e78-252">Získat kód</span><span class="sxs-lookup"><span data-stu-id="64e78-252">Get the code</span></span>

[<span data-ttu-id="64e78-253">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="64e78-253">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="64e78-254">Další kroky</span><span class="sxs-lookup"><span data-stu-id="64e78-254">Next steps</span></span>

<span data-ttu-id="64e78-255">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="64e78-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="64e78-256">Přidaný sloupec řazení odkazy</span><span class="sxs-lookup"><span data-stu-id="64e78-256">Added column sort links</span></span>
> * <span data-ttu-id="64e78-257">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="64e78-257">Added a Search box</span></span>
> * <span data-ttu-id="64e78-258">Přidání stránkování pro studenty indexu</span><span class="sxs-lookup"><span data-stu-id="64e78-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="64e78-259">Přidání stránkování k Index – metoda</span><span class="sxs-lookup"><span data-stu-id="64e78-259">Added paging to Index method</span></span>
> * <span data-ttu-id="64e78-260">Přidání odkazů stránkování</span><span class="sxs-lookup"><span data-stu-id="64e78-260">Added paging links</span></span>
> * <span data-ttu-id="64e78-261">Vytvoří stránku o</span><span class="sxs-lookup"><span data-stu-id="64e78-261">Created an About page</span></span>

<span data-ttu-id="64e78-262">Přejděte k dalšímu kurzu se naučíte zpracování změn datového modelu s použitím migrace.</span><span class="sxs-lookup"><span data-stu-id="64e78-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="64e78-263">Další: Zpracování změn datových modelů</span><span class="sxs-lookup"><span data-stu-id="64e78-263">Next: Handle data model changes</span></span>](migrations.md)
