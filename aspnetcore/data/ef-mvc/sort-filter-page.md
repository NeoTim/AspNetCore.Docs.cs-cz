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
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="52634-104">Kurz: Přidání řazení, filtrování a stránkování – ASP.NET MVC s EF core</span><span class="sxs-lookup"><span data-stu-id="52634-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="52634-105">V předchozím kurzu jste implementovali sadu webových stránek pro základní operace CRUD pro studentské entity.</span><span class="sxs-lookup"><span data-stu-id="52634-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="52634-106">V tomto kurzu přidáte funkce řazení, filtrování a stránkování na stránku Students Index.</span><span class="sxs-lookup"><span data-stu-id="52634-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="52634-107">Vytvoříte také stránku, která provádí jednoduché seskupení.</span><span class="sxs-lookup"><span data-stu-id="52634-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="52634-108">Následující obrázek znázorňuje, jak bude stránka vypadat, až budete hotovi.</span><span class="sxs-lookup"><span data-stu-id="52634-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="52634-109">Záhlaví sloupců jsou odkazy, na které může uživatel kliknout a seřadit je podle tohoto sloupce.</span><span class="sxs-lookup"><span data-stu-id="52634-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="52634-110">Opakovaným klepnutím na záhlaví sloupce přepínáte mezi vzestupným a sestupným pořadím řazení.</span><span class="sxs-lookup"><span data-stu-id="52634-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Stránka indexu studentů](sort-filter-page/_static/paging.png)

<span data-ttu-id="52634-112">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="52634-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="52634-113">Přidání odkazů řazení sloupců</span><span class="sxs-lookup"><span data-stu-id="52634-113">Add column sort links</span></span>
> * <span data-ttu-id="52634-114">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="52634-114">Add a Search box</span></span>
> * <span data-ttu-id="52634-115">Přidání stránkování do indexu studentů</span><span class="sxs-lookup"><span data-stu-id="52634-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="52634-116">Přidat stránkování do metody Index</span><span class="sxs-lookup"><span data-stu-id="52634-116">Add paging to Index method</span></span>
> * <span data-ttu-id="52634-117">Přidání stránkovacích odkazů</span><span class="sxs-lookup"><span data-stu-id="52634-117">Add paging links</span></span>
> * <span data-ttu-id="52634-118">Vytvořit stránku Informace</span><span class="sxs-lookup"><span data-stu-id="52634-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="52634-119">Požadavky</span><span class="sxs-lookup"><span data-stu-id="52634-119">Prerequisites</span></span>

* [<span data-ttu-id="52634-120">Implementace funkcí CRUD</span><span class="sxs-lookup"><span data-stu-id="52634-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="52634-121">Přidání odkazů řazení sloupců</span><span class="sxs-lookup"><span data-stu-id="52634-121">Add column sort links</span></span>

<span data-ttu-id="52634-122">Chcete-li přidat řazení na stránku Index `Index` studentů, změníte metodu ovladače Studenti a přidáte kód do zobrazení StudentIndex.</span><span class="sxs-lookup"><span data-stu-id="52634-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="52634-123">Přidání funkce řazení do metody Index</span><span class="sxs-lookup"><span data-stu-id="52634-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="52634-124">V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="52634-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="52634-125">Tento kód obdrží `sortOrder` parametr z řetězce dotazu v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="52634-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="52634-126">Hodnota řetězce dotazu je poskytována ASP.NET Core MVC jako parametr metody akce.</span><span class="sxs-lookup"><span data-stu-id="52634-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="52634-127">Parametr bude řetězec, který je buď "Název" nebo "Datum", volitelně následuje podtržítko a řetězec "desc" určit sestupné pořadí.</span><span class="sxs-lookup"><span data-stu-id="52634-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="52634-128">Výchozí pořadí řazení je vzestupně.</span><span class="sxs-lookup"><span data-stu-id="52634-128">The default sort order is ascending.</span></span>

<span data-ttu-id="52634-129">Při prvním požadavku na stránku Index neexistuje žádný řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="52634-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="52634-130">Studenti jsou zobrazeny ve vzestupném pořadí podle příjmení, což je výchozí hodnota `switch` stanovená v případě pádu v prohlášení.</span><span class="sxs-lookup"><span data-stu-id="52634-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="52634-131">Když uživatel klepne na hypertextový odkaz `sortOrder` záhlaví sloupce, je v řetězci dotazu uvedena příslušná hodnota.</span><span class="sxs-lookup"><span data-stu-id="52634-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="52634-132">Dva `ViewData` prvky (NameSortParm a DateSortParm) jsou používány zobrazením ke konfiguraci hypertextových odkazů záhlaví sloupce s příslušnými hodnotami řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="52634-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="52634-133">Tohle jsou ternární prohlášení.</span><span class="sxs-lookup"><span data-stu-id="52634-133">These are ternary statements.</span></span> <span data-ttu-id="52634-134">První z nich určuje, `sortOrder` že pokud je parametr null nebo prázdný, NameSortParm by měl být nastaven na "name_desc"; v opačném případě by měla být nastavena na prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="52634-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="52634-135">Tyto dva příkazy umožňují zobrazení nastavit hypertextové odkazy záhlaví sloupců takto:</span><span class="sxs-lookup"><span data-stu-id="52634-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="52634-136">Aktuální pořadí řazení</span><span class="sxs-lookup"><span data-stu-id="52634-136">Current sort order</span></span>  | <span data-ttu-id="52634-137">Hypertextový odkaz příjmení</span><span class="sxs-lookup"><span data-stu-id="52634-137">Last Name Hyperlink</span></span> | <span data-ttu-id="52634-138">Hypertextový odkaz data</span><span class="sxs-lookup"><span data-stu-id="52634-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="52634-139">Příjmení vzestupně</span><span class="sxs-lookup"><span data-stu-id="52634-139">Last Name ascending</span></span>  | <span data-ttu-id="52634-140">descending</span><span class="sxs-lookup"><span data-stu-id="52634-140">descending</span></span>          | <span data-ttu-id="52634-141">ascending</span><span class="sxs-lookup"><span data-stu-id="52634-141">ascending</span></span>      |
| <span data-ttu-id="52634-142">Příjmení sestupně</span><span class="sxs-lookup"><span data-stu-id="52634-142">Last Name descending</span></span> | <span data-ttu-id="52634-143">ascending</span><span class="sxs-lookup"><span data-stu-id="52634-143">ascending</span></span>           | <span data-ttu-id="52634-144">ascending</span><span class="sxs-lookup"><span data-stu-id="52634-144">ascending</span></span>      |
| <span data-ttu-id="52634-145">Datum vzestupně</span><span class="sxs-lookup"><span data-stu-id="52634-145">Date ascending</span></span>       | <span data-ttu-id="52634-146">ascending</span><span class="sxs-lookup"><span data-stu-id="52634-146">ascending</span></span>           | <span data-ttu-id="52634-147">descending</span><span class="sxs-lookup"><span data-stu-id="52634-147">descending</span></span>     |
| <span data-ttu-id="52634-148">Datum sestupně</span><span class="sxs-lookup"><span data-stu-id="52634-148">Date descending</span></span>      | <span data-ttu-id="52634-149">ascending</span><span class="sxs-lookup"><span data-stu-id="52634-149">ascending</span></span>           | <span data-ttu-id="52634-150">ascending</span><span class="sxs-lookup"><span data-stu-id="52634-150">ascending</span></span>      |

<span data-ttu-id="52634-151">Metoda používá LINQ entity k určení sloupce, podle kterého se má řadit.</span><span class="sxs-lookup"><span data-stu-id="52634-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="52634-152">Kód vytvoří `IQueryable` proměnnou před příkazem switch, upraví ji v příkazu switch a zavolá metodu `ToListAsync` za příkazem. `switch`</span><span class="sxs-lookup"><span data-stu-id="52634-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="52634-153">Při vytváření a `IQueryable` úpravě proměnných není do databáze odeslán žádný dotaz.</span><span class="sxs-lookup"><span data-stu-id="52634-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="52634-154">Dotaz není proveden, dokud převést `IQueryable` objekt do kolekce voláním `ToListAsync`metody, jako je například .</span><span class="sxs-lookup"><span data-stu-id="52634-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="52634-155">Proto tento kód má za následek jeden dotaz, `return View` který není proveden až do příkazu.</span><span class="sxs-lookup"><span data-stu-id="52634-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="52634-156">Tento kód může získat podrobné s velkým počtem sloupců.</span><span class="sxs-lookup"><span data-stu-id="52634-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="52634-157">[Poslední kurz v této sérii](advanced.md#dynamic-linq) ukazuje, jak psát kód, `OrderBy` který umožňuje předat název sloupce v proměnné řetězce.</span><span class="sxs-lookup"><span data-stu-id="52634-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="52634-158">Přidání hypertextových odkazů záhlaví sloupců do zobrazení Studentský index</span><span class="sxs-lookup"><span data-stu-id="52634-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="52634-159">Nahraďte kód v *zobrazení/Studenti/Index.cshtml*, s následujícím kódem přidat záhlaví sloupce hypertextové odkazy.</span><span class="sxs-lookup"><span data-stu-id="52634-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="52634-160">Změněné řádky jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="52634-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="52634-161">Tento kód používá `ViewData` informace ve vlastnostech k nastavení hypertextových odkazů s příslušnými hodnotami řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="52634-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="52634-162">Spusťte aplikaci, vyberte kartu **Studenti** a kliknutím na záhlaví sloupců **Příjmení** a **Datum registrace** ověřte, zda řazení funguje.</span><span class="sxs-lookup"><span data-stu-id="52634-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Studenty index stránky v pořadí názvů](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="52634-164">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="52634-164">Add a Search box</span></span>

<span data-ttu-id="52634-165">Chcete-li přidat filtrování na stránku Students Index, přidáte do zobrazení textové pole a `Index` tlačítko odeslat a provedete odpovídající změny v metodě.</span><span class="sxs-lookup"><span data-stu-id="52634-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="52634-166">Textové pole vám umožní zadat řetězec, který chcete vyhledat v polích křestníjméno a příjmení.</span><span class="sxs-lookup"><span data-stu-id="52634-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="52634-167">Přidání funkce filtrování do metody Index</span><span class="sxs-lookup"><span data-stu-id="52634-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="52634-168">V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem (změny jsou zvýrazněny).</span><span class="sxs-lookup"><span data-stu-id="52634-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="52634-169">Přidali jste `searchString` parametr do `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="52634-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="52634-170">Hodnota hledaného řetězce je přijata z textového pole, které přidáte do zobrazení rejstříku.</span><span class="sxs-lookup"><span data-stu-id="52634-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="52634-171">Přidali jste také do příkazu LINQ klauzule where, která vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="52634-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="52634-172">Příkaz, který přidá klauzule where je spuštěna pouze v případě, že je hodnota hledat.</span><span class="sxs-lookup"><span data-stu-id="52634-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="52634-173">Zde voláte `Where` metodu `IQueryable` na objektu a filtr bude zpracován na serveru.</span><span class="sxs-lookup"><span data-stu-id="52634-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="52634-174">V některých případech může `Where` být volání metody jako metoda rozšíření v kolekci v paměti.</span><span class="sxs-lookup"><span data-stu-id="52634-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="52634-175">(Předpokládejme například, že `_context.Students` změníte odkaz na `DbSet` tak, aby namísto EF `IEnumerable` odkazuje na metodu úložiště, která vrací kolekci.) Výsledek by za normálních okolností byl stejný, ale v některých případech může být odlišný.</span><span class="sxs-lookup"><span data-stu-id="52634-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="52634-176">Například implementace `Contains` rozhraní .NET Framework metody provádí porovnání rozlišování malých a velkých písmen ve výchozím nastavení, ale v SQL Server je to určeno nastavení řazení instance serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="52634-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="52634-177">Toto nastavení je výchozí pro nerozlišování velkých a malých písmen.</span><span class="sxs-lookup"><span data-stu-id="52634-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="52634-178">Můžete volat `ToUpper` metodu, aby test explicitně malá a velká písmena: *Kde (s => s.LastName.ToUpper(). Contains(searchString.ToUpper())*.</span><span class="sxs-lookup"><span data-stu-id="52634-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="52634-179">To by zajistilo, že výsledky zůstanou stejné, pokud změníte `IEnumerable` kód později použít úložiště, které vrátí kolekci namísto objektu. `IQueryable`</span><span class="sxs-lookup"><span data-stu-id="52634-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="52634-180">(Při volání `Contains` metody na `IEnumerable` kolekci získáte implementaci rozhraní .NET Framework; `IQueryable` při volání na objekt, získáte implementaci zprostředkovatele databáze.) Pro toto řešení je však k dispozici snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="52634-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="52634-181">Kód `ToUpper` by dal funkci do klauzule WHERE příkazu TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="52634-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="52634-182">To by zabránilo optimalizátoru používat index.</span><span class="sxs-lookup"><span data-stu-id="52634-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="52634-183">Vzhledem k tomu, že SQL je většinou nainstalován `ToUpper` jako malá a velká písmena, je nejlepší se vyhnout kódu, dokud migrovat do úložiště dat rozlišování velkých a malých písmen.</span><span class="sxs-lookup"><span data-stu-id="52634-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="52634-184">Přidání vyhledávacího pole do zobrazení studentského indexu</span><span class="sxs-lookup"><span data-stu-id="52634-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="52634-185">V *části Zobrazení/Student/Index.cshtml*přidejte zvýrazněný kód bezprostředně před značku tabulky pro otevření, abyste vytvořili titulek, textové pole a tlačítko **Hledat.**</span><span class="sxs-lookup"><span data-stu-id="52634-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="52634-186">Tento kód `<form>` používá [pomocníka pro označení](xref:mvc/views/tag-helpers/intro) k přidání vyhledávacího textového pole a tlačítka.</span><span class="sxs-lookup"><span data-stu-id="52634-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="52634-187">Ve výchozím `<form>` nastavení odešle pomocná značka data formuláře s post, což znamená, že parametry jsou předávány v textu zprávy HTTP a nikoli v adrese URL jako řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="52634-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="52634-188">Když zadáte HTTP GET, data formuláře jsou předána v adrese URL jako řetězce dotazu, což umožňuje uživatelům záložku url.</span><span class="sxs-lookup"><span data-stu-id="52634-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="52634-189">W3C pokyny doporučujeme použít GET, pokud akce nemá za následek aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="52634-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="52634-190">Spusťte aplikaci, vyberte kartu **Studenti,** zadejte vyhledávací řetězec a kliknutím na Hledat ověřte, zda filtrování funguje.</span><span class="sxs-lookup"><span data-stu-id="52634-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Stránka indexu studentů s filtrováním](sort-filter-page/_static/filtering.png)

<span data-ttu-id="52634-192">Všimněte si, že adresa URL obsahuje hledaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="52634-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="52634-193">Pokud si tuto stránku přidáte do záložek, dostanete při použití záložky filtrovaný seznam.</span><span class="sxs-lookup"><span data-stu-id="52634-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="52634-194">Přidání `method="get"` do `form` značky je to, co způsobilo, že řetězec dotazu byl generován.</span><span class="sxs-lookup"><span data-stu-id="52634-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="52634-195">V této fázi, pokud kliknete na odkaz řazení záhlaví sloupce, ztratíte hodnotu filtru, kterou jste zadali do pole **Hledat.**</span><span class="sxs-lookup"><span data-stu-id="52634-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="52634-196">Opravíte to v další části.</span><span class="sxs-lookup"><span data-stu-id="52634-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="52634-197">Přidání stránkování do indexu studentů</span><span class="sxs-lookup"><span data-stu-id="52634-197">Add paging to Students Index</span></span>

<span data-ttu-id="52634-198">Chcete-li přidat stránku Studentova indexu, `PaginatedList` vytvoříte `Skip` třídu, která používá a `Take` příkazy k filtrování dat na serveru namísto toho, abyste vždy načítali všechny řádky tabulky.</span><span class="sxs-lookup"><span data-stu-id="52634-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="52634-199">Potom provedete další změny `Index` v metodě a přidáte `Index` do zobrazení tlačítka stránkování.</span><span class="sxs-lookup"><span data-stu-id="52634-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="52634-200">Následující obrázek znázorňuje tlačítka stránkování.</span><span class="sxs-lookup"><span data-stu-id="52634-200">The following illustration shows the paging buttons.</span></span>

![Stránka indexu studentů s pagingovými odkazy](sort-filter-page/_static/paging.png)

<span data-ttu-id="52634-202">Ve složce projektu `PaginatedList.cs`vytvořte a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="52634-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="52634-203">Metoda `CreateAsync` v tomto kódu přebírá velikost stránky a `Skip` číslo `Take` stránky `IQueryable`a použije příslušné příkazy pro .</span><span class="sxs-lookup"><span data-stu-id="52634-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="52634-204">Když `ToListAsync` je volána na `IQueryable`, vrátí Seznam obsahující pouze požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="52634-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="52634-205">Vlastnosti `HasPreviousPage` `HasNextPage` a lze použít k povolení nebo zakázání **předchozí** a **další** stránkovací tlačítka.</span><span class="sxs-lookup"><span data-stu-id="52634-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="52634-206">Metoda `CreateAsync` se používá místo konstruktoru `PaginatedList<T>` k vytvoření objektu, protože konstruktory nelze spustit asynchronní kód.</span><span class="sxs-lookup"><span data-stu-id="52634-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="52634-207">Přidat stránkování do metody Index</span><span class="sxs-lookup"><span data-stu-id="52634-207">Add paging to Index method</span></span>

<span data-ttu-id="52634-208">V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="52634-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="52634-209">Tento kód přidá k podpisu metody parametr čísla stránky, aktuální parametr pořadí řazení a aktuální parametr filtru.</span><span class="sxs-lookup"><span data-stu-id="52634-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="52634-210">Při prvním zobrazení stránky nebo při kliknutí na stránkovací nebo seřazený odkaz má uživatel hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="52634-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="52634-211">Pokud klepnete na odkaz stránkování, proměnná stránky bude obsahovat číslo stránky, které se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="52634-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="52634-212">Prvek `ViewData` s názvem CurrentSort poskytuje zobrazení s aktuální pořadí řazení, protože to musí být zahrnuty do stránkování odkazy, aby bylo možné zachovat pořadí řazení stejné při stránkování.</span><span class="sxs-lookup"><span data-stu-id="52634-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="52634-213">Prvek `ViewData` s názvem CurrentFilter poskytuje zobrazení s aktuálnířetězec filtru.</span><span class="sxs-lookup"><span data-stu-id="52634-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="52634-214">Tato hodnota musí být zahrnuta do stránkovacích odkazů, aby bylo zachováno nastavení filtru během stránkování, a musí být obnovena do textového pole při opětovném zobrazení stránky.</span><span class="sxs-lookup"><span data-stu-id="52634-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="52634-215">Pokud se vyhledávací řetězec během stránkování změní, musí být stránka obnovena na hodnotu 1, protože nový filtr může vést k zobrazení různých dat.</span><span class="sxs-lookup"><span data-stu-id="52634-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="52634-216">Hledaný řetězec se změní, když je do textového pole zadána hodnota a je stisknuto tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="52634-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="52634-217">V takovém případě `searchString` parametr není null.</span><span class="sxs-lookup"><span data-stu-id="52634-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="52634-218">Na konci `Index` metody `PaginatedList.CreateAsync` metoda převede studentdotaz na jednu stránku studentů v typu kolekce, která podporuje stránkování.</span><span class="sxs-lookup"><span data-stu-id="52634-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="52634-219">Tato jediná stránka studentů je pak předána do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="52634-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="52634-220">Metoda `PaginatedList.CreateAsync` přebírá číslo stránky.</span><span class="sxs-lookup"><span data-stu-id="52634-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="52634-221">Tyto dva otazníky představují operátor null-coalescing.</span><span class="sxs-lookup"><span data-stu-id="52634-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="52634-222">Operátor null-coalescing definuje výchozí hodnotu pro typ s možnou hodnotou null; výraz `(pageNumber ?? 1)` znamená vrátit hodnotu, `pageNumber` pokud má hodnotu, `pageNumber` nebo vrátit 1, pokud je null.</span><span class="sxs-lookup"><span data-stu-id="52634-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="52634-223">Přidání stránkovacích odkazů</span><span class="sxs-lookup"><span data-stu-id="52634-223">Add paging links</span></span>

<span data-ttu-id="52634-224">V *části Zobrazení/Studenti/Index.cshtml*nahraďte existující kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="52634-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="52634-225">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="52634-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="52634-226">Příkaz `@model` v horní části stránky určuje, že zobrazení `PaginatedList<T>` nyní získá `List<T>` objekt namísto objektu.</span><span class="sxs-lookup"><span data-stu-id="52634-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="52634-227">Odkazy záhlaví sloupce používají řetězec dotazu k předání aktuálního vyhledávacího řetězce řadiči, aby uživatel mohl řadit ve výsledcích filtru:</span><span class="sxs-lookup"><span data-stu-id="52634-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="52634-228">Tlačítka stránkování jsou zobrazena pomocníky značek:</span><span class="sxs-lookup"><span data-stu-id="52634-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="52634-229">Spusťte aplikaci a přejděte na stránku Studenti.</span><span class="sxs-lookup"><span data-stu-id="52634-229">Run the app and go to the Students page.</span></span>

![Stránka indexu studentů s pagingovými odkazy](sort-filter-page/_static/paging.png)

<span data-ttu-id="52634-231">Chcete-li zajistit, aby stránkování fungovalo, klepněte na stránkovací odkazy v různých pořadích řazení.</span><span class="sxs-lookup"><span data-stu-id="52634-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="52634-232">Potom zadejte hledaný řetězec a zkuste stránkování znovu ověřit, že stránkování funguje také správně s řazením a filtrováním.</span><span class="sxs-lookup"><span data-stu-id="52634-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="52634-233">Vytvořit stránku Informace</span><span class="sxs-lookup"><span data-stu-id="52634-233">Create an About page</span></span>

<span data-ttu-id="52634-234">Na stránce **Informace o** webu Univerzity Contoso zobrazíte, kolik studentů se zaregistrovalo pro každé datum registrace.</span><span class="sxs-lookup"><span data-stu-id="52634-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="52634-235">To vyžaduje seskupení a jednoduché výpočty na skupinách.</span><span class="sxs-lookup"><span data-stu-id="52634-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="52634-236">Chcete-li toho dosáhnout, proveďte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="52634-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="52634-237">Vytvořte třídu modelu zobrazení pro data, která je třeba předat do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="52634-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="52634-238">Vytvořte metodu O kontroleci.</span><span class="sxs-lookup"><span data-stu-id="52634-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="52634-239">Vytvořte zobrazení Informace.</span><span class="sxs-lookup"><span data-stu-id="52634-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="52634-240">Vytvoření modelu pohledu</span><span class="sxs-lookup"><span data-stu-id="52634-240">Create the view model</span></span>

<span data-ttu-id="52634-241">Vytvořte složku *SchoolViewModels* ve složce *Modely.*</span><span class="sxs-lookup"><span data-stu-id="52634-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="52634-242">V nové složce přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="52634-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="52634-243">Úprava domácího ovladače</span><span class="sxs-lookup"><span data-stu-id="52634-243">Modify the Home Controller</span></span>

<span data-ttu-id="52634-244">V *HomeController.cs*přidejte do horní části souboru následující příkazy pomocí:</span><span class="sxs-lookup"><span data-stu-id="52634-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="52634-245">Přidejte proměnnou třídy pro kontext databáze bezprostředně po otevření složené závorky pro třídu a získejte instanci kontextu z ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="52634-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="52634-246">Přidejte `About` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="52634-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="52634-247">Příkaz LINQ seskupuje entity studenta podle data zápisu, vypočítá počet entit v každé `EnrollmentDateGroup` skupině a uloží výsledky do kolekce objektů modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="52634-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="52634-248">Vytvoření zobrazení O informace</span><span class="sxs-lookup"><span data-stu-id="52634-248">Create the About View</span></span>

<span data-ttu-id="52634-249">Přidejte soubor *Views/Home/About.cshtml* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="52634-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="52634-250">Spusťte aplikaci a přejděte na stránku Informace.</span><span class="sxs-lookup"><span data-stu-id="52634-250">Run the app and go to the About page.</span></span> <span data-ttu-id="52634-251">Počet studentů pro každé datum zápisu se zobrazí v tabulce.</span><span class="sxs-lookup"><span data-stu-id="52634-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="52634-252">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="52634-252">Get the code</span></span>

[<span data-ttu-id="52634-253">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="52634-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="52634-254">Další kroky</span><span class="sxs-lookup"><span data-stu-id="52634-254">Next steps</span></span>

<span data-ttu-id="52634-255">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="52634-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="52634-256">Přidány odkazy na řazení sloupců</span><span class="sxs-lookup"><span data-stu-id="52634-256">Added column sort links</span></span>
> * <span data-ttu-id="52634-257">Přidáno vyhledávací pole</span><span class="sxs-lookup"><span data-stu-id="52634-257">Added a Search box</span></span>
> * <span data-ttu-id="52634-258">Přidáno stránkování do indexu studentů</span><span class="sxs-lookup"><span data-stu-id="52634-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="52634-259">Přidáno stránkování do metody Index</span><span class="sxs-lookup"><span data-stu-id="52634-259">Added paging to Index method</span></span>
> * <span data-ttu-id="52634-260">Přidány stránkovací odkazy</span><span class="sxs-lookup"><span data-stu-id="52634-260">Added paging links</span></span>
> * <span data-ttu-id="52634-261">Vytvoření stránky Informace</span><span class="sxs-lookup"><span data-stu-id="52634-261">Created an About page</span></span>

<span data-ttu-id="52634-262">Přejdete k dalšímu kurzu, kde se dozvíte, jak zpracovat změny datového modelu pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="52634-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="52634-263">Další: Zpracování změn datového modelu</span><span class="sxs-lookup"><span data-stu-id="52634-263">Next: Handle data model changes</span></span>](migrations.md)
