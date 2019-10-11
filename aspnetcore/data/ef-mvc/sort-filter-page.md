---
title: 'Kurz: Přidání řazení, filtrování a stránkování – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu přidáte na stránku indexu studentů funkce řazení, filtrování a stránkování. Vytvoří se také stránka, která provede jednoduché seskupení.
author: rick-anderson
ms.author: riande
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: c4d50b72c5508d52b17c6754b6d8e77c1a3903b6
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259341"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="d7bfd-104">Kurz: Přidání řazení, filtrování a stránkování – ASP.NET MVC pomocí EF Core</span><span class="sxs-lookup"><span data-stu-id="d7bfd-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="d7bfd-105">V předchozím kurzu jste implementovali sadu webových stránek pro základní operace CRUD pro entity studenta.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="d7bfd-106">V tomto kurzu přidáte na stránku indexu studentů funkce řazení, filtrování a stránkování.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="d7bfd-107">Vytvoří se také stránka, která provede jednoduché seskupení.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="d7bfd-108">Na následujícím obrázku vidíte, jak stránka bude vypadat, až budete hotovi.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="d7bfd-109">Záhlaví sloupců jsou odkazy, na které může uživatel kliknout pro řazení podle daného sloupce.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="d7bfd-110">Kliknutí na záhlaví sloupce se opakovaně přepíná mezi vzestupném a sestupným řazením.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Stránka indexu studentů](sort-filter-page/_static/paging.png)

<span data-ttu-id="d7bfd-112">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d7bfd-113">Přidat odkazy na řazení sloupců</span><span class="sxs-lookup"><span data-stu-id="d7bfd-113">Add column sort links</span></span>
> * <span data-ttu-id="d7bfd-114">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="d7bfd-114">Add a Search box</span></span>
> * <span data-ttu-id="d7bfd-115">Přidat stránkování do indexu studentů</span><span class="sxs-lookup"><span data-stu-id="d7bfd-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="d7bfd-116">Přidat stránkování do metody indexu</span><span class="sxs-lookup"><span data-stu-id="d7bfd-116">Add paging to Index method</span></span>
> * <span data-ttu-id="d7bfd-117">Přidat odkazy na stránkování</span><span class="sxs-lookup"><span data-stu-id="d7bfd-117">Add paging links</span></span>
> * <span data-ttu-id="d7bfd-118">Vytvoření stránky o stránku</span><span class="sxs-lookup"><span data-stu-id="d7bfd-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d7bfd-119">Požadované součásti</span><span class="sxs-lookup"><span data-stu-id="d7bfd-119">Prerequisites</span></span>

* [<span data-ttu-id="d7bfd-120">Implementace funkce CRUD</span><span class="sxs-lookup"><span data-stu-id="d7bfd-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="d7bfd-121">Přidat odkazy na řazení sloupců</span><span class="sxs-lookup"><span data-stu-id="d7bfd-121">Add column sort links</span></span>

<span data-ttu-id="d7bfd-122">Chcete-li přidat řazení na stránku indexu studenta, změňte metodu `Index` řadiče Students a přidejte kód do zobrazení indexu studenta.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="d7bfd-123">Přidání funkcí řazení do metody index</span><span class="sxs-lookup"><span data-stu-id="d7bfd-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="d7bfd-124">V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="d7bfd-125">Tento kód obdrží parametr `sortOrder` z řetězce dotazu v adrese URL.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="d7bfd-126">Hodnota řetězce dotazu je poskytnuta ASP.NET Core MVC jako parametr metody Action.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="d7bfd-127">Parametr bude řetězec, který je buď "Name", nebo "date", volitelně následovaný podtržítkem a řetězcem "desc" pro určení sestupného pořadí.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="d7bfd-128">Výchozí pořadí řazení je vzestupné.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-128">The default sort order is ascending.</span></span>

<span data-ttu-id="d7bfd-129">Při prvním vyžádání stránky indexu není k dispozici žádný řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="d7bfd-130">Studenti se zobrazí ve vzestupném pořadí podle příjmení, což je výchozí nastavení zavedené v případě příkazu `switch`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="d7bfd-131">Když uživatel klikne na hypertextový odkaz záhlaví sloupce, v řetězci dotazu je uvedena odpovídající hodnota `sortOrder`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="d7bfd-132">Dva prvky `ViewData` (NameSortParm a DateSortParm) jsou používány zobrazením ke konfiguraci hypertextových odkazů záhlaví sloupce odpovídajícím hodnotám řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="d7bfd-133">Jedná se o Ternární příkazy.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-133">These are ternary statements.</span></span> <span data-ttu-id="d7bfd-134">První z nich určuje, že pokud parametr `sortOrder` má hodnotu null nebo je prázdný, NameSortParm by měl být nastaven na "name_desc"; v opačném případě by měl být nastaven na prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="d7bfd-135">Tyto dva příkazy umožňují zobrazení nastavit hypertextové odkazy záhlaví sloupce následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="d7bfd-136">Aktuální pořadí řazení</span><span class="sxs-lookup"><span data-stu-id="d7bfd-136">Current sort order</span></span>  | <span data-ttu-id="d7bfd-137">Hypertextový odkaz na poslední jméno</span><span class="sxs-lookup"><span data-stu-id="d7bfd-137">Last Name Hyperlink</span></span> | <span data-ttu-id="d7bfd-138">Hypertextový odkaz na datum</span><span class="sxs-lookup"><span data-stu-id="d7bfd-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="d7bfd-139">Příjmení vzestupné</span><span class="sxs-lookup"><span data-stu-id="d7bfd-139">Last Name ascending</span></span>  | <span data-ttu-id="d7bfd-140">vzestupné</span><span class="sxs-lookup"><span data-stu-id="d7bfd-140">descending</span></span>          | <span data-ttu-id="d7bfd-141">vzestupně</span><span class="sxs-lookup"><span data-stu-id="d7bfd-141">ascending</span></span>      |
| <span data-ttu-id="d7bfd-142">Příjmení sestupně</span><span class="sxs-lookup"><span data-stu-id="d7bfd-142">Last Name descending</span></span> | <span data-ttu-id="d7bfd-143">vzestupně</span><span class="sxs-lookup"><span data-stu-id="d7bfd-143">ascending</span></span>           | <span data-ttu-id="d7bfd-144">vzestupně</span><span class="sxs-lookup"><span data-stu-id="d7bfd-144">ascending</span></span>      |
| <span data-ttu-id="d7bfd-145">Datum vzestupné</span><span class="sxs-lookup"><span data-stu-id="d7bfd-145">Date ascending</span></span>       | <span data-ttu-id="d7bfd-146">vzestupně</span><span class="sxs-lookup"><span data-stu-id="d7bfd-146">ascending</span></span>           | <span data-ttu-id="d7bfd-147">vzestupné</span><span class="sxs-lookup"><span data-stu-id="d7bfd-147">descending</span></span>     |
| <span data-ttu-id="d7bfd-148">Datum sestupné</span><span class="sxs-lookup"><span data-stu-id="d7bfd-148">Date descending</span></span>      | <span data-ttu-id="d7bfd-149">vzestupně</span><span class="sxs-lookup"><span data-stu-id="d7bfd-149">ascending</span></span>           | <span data-ttu-id="d7bfd-150">vzestupně</span><span class="sxs-lookup"><span data-stu-id="d7bfd-150">ascending</span></span>      |

<span data-ttu-id="d7bfd-151">Metoda používá LINQ to Entities k určení sloupce, podle kterého se má řadit.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="d7bfd-152">Kód vytvoří proměnnou `IQueryable` před příkazem Switch, upraví ji v příkazu switch a zavolá metodu `ToListAsync` po příkazu `switch`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="d7bfd-153">Když vytvoříte a upravíte proměnné `IQueryable`, do databáze se nepošle žádný dotaz.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="d7bfd-154">Dotaz není proveden, dokud nepřevedete objekt `IQueryable` do kolekce voláním metody, jako je například `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="d7bfd-155">Proto tento kód má za následek jeden dotaz, který se neprovede až do příkazu `return View`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="d7bfd-156">Tento kód může získat podrobné zobrazení velkého počtu sloupců.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="d7bfd-157">[Poslední kurz v této sérii](advanced.md#dynamic-linq) ukazuje, jak napsat kód, který vám umožní předat název sloupce `OrderBy` v proměnné typu řetězec.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="d7bfd-158">Přidat hypertextové odkazy záhlaví sloupce do zobrazení indexu studenta</span><span class="sxs-lookup"><span data-stu-id="d7bfd-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="d7bfd-159">Nahraďte kód v *zobrazeních/Students/index. cshtml*s následujícím kódem pro přidání hypertextových odkazů záhlaví sloupců.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="d7bfd-160">Změněné řádky jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="d7bfd-161">Tento kód používá informace ve vlastnostech `ViewData` k nastavení hypertextových odkazů s příslušnými hodnotami řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="d7bfd-162">Spusťte aplikaci, vyberte kartu **studenti** a kliknutím na záhlaví sloupce Datum **poslední jméno** a **Datum registrace** ověřte, že řazení funguje.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Stránka indexu studentů v pořadí podle názvu](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="d7bfd-164">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="d7bfd-164">Add a Search box</span></span>

<span data-ttu-id="d7bfd-165">Chcete-li přidat filtrování na stránku indexu studentů, přidejte do zobrazení textové pole a tlačítko Odeslat a proveďte odpovídající změny v metodě `Index`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="d7bfd-166">Textové pole umožňuje zadat řetězec, který bude hledán v poli jméno a příjmení.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="d7bfd-167">Přidání funkce filtrování do metody index</span><span class="sxs-lookup"><span data-stu-id="d7bfd-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="d7bfd-168">V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem (změny jsou zvýrazněny).</span><span class="sxs-lookup"><span data-stu-id="d7bfd-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="d7bfd-169">Do metody `Index` jste přidali parametr `searchString`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="d7bfd-170">Hodnota vyhledávacího řetězce je přijímána z textového pole, které přidáte do zobrazení index.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="d7bfd-171">Také jste přidali do příkazu LINQ klauzule WHERE, která vybere pouze studenty, jejichž křestní jméno nebo příjmení obsahuje hledaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="d7bfd-172">Příkaz, který přidá klauzuli WHERE, je proveden pouze v případě, že existuje hodnota, která se má vyhledat.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="d7bfd-173">Zde zavoláte metodu `Where` u objektu `IQueryable` a filtr bude zpracován na serveru.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="d7bfd-174">V některých scénářích můžete volat metodu `Where` jako metodu rozšíření v kolekci v paměti.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="d7bfd-175">(Předpokládejme například, že změníte odkaz na `_context.Students` tak, aby místo EF `DbSet` odkazovala na metodu úložiště, která vrací kolekci `IEnumerable`.) Výsledek by byl normálně stejný, ale v některých případech se může lišit.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="d7bfd-176">Například implementace .NET Framework metody `Contains` má ve výchozím nastavení porovnávání citlivé na velká a malá písmena, ale v SQL Server to je určeno nastavením kolace instance SQL Server.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="d7bfd-177">Ve výchozím nastavení se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="d7bfd-178">Můžete zavolat metodu `ToUpper` k tomu, aby test explicitně necitlivý na velká písmena: *WHERE (s = > s. LastName. ToUpper (). Obsahuje (searchString. ToUpper ())* .</span><span class="sxs-lookup"><span data-stu-id="d7bfd-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="d7bfd-179">Aby bylo zajištěno, že výsledky zůstanou stejné, pokud později změníte kód, aby používal úložiště, které vrací kolekci `IEnumerable` namísto objektu `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="d7bfd-180">(Když zavoláte metodu `Contains` v kolekci `IEnumerable`, získáte .NET Framework implementaci. při volání na objekt `IQueryable` získáte implementaci poskytovatele databáze.) Pro toto řešení však existuje snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="d7bfd-181">Kód `ToUpper` by znamenal funkci v klauzuli WHERE příkazu TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="d7bfd-182">Tím zabráníte Optimalizátoru v používání indexu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="d7bfd-183">Vzhledem k tomu, že SQL je většinou nainstalován jako nerozlišovat velká a malá písmena, je nejlepší se vyhnout kódu `ToUpper`, dokud nemigrujete do úložiště dat citlivého na velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="d7bfd-184">Přidání vyhledávacího pole do zobrazení indexu studenta</span><span class="sxs-lookup"><span data-stu-id="d7bfd-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="d7bfd-185">V *zobrazení/student/index. cshtml*přidejte zvýrazněný kód bezprostředně před levou značku tabulky, aby bylo možné vytvořit titulek, textové pole a tlačítko **hledání** .</span><span class="sxs-lookup"><span data-stu-id="d7bfd-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="d7bfd-186">Tento kód pomocí [pomocníka značek](xref:mvc/views/tag-helpers/intro) `<form>` přidá textové pole a tlačítko hledání.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="d7bfd-187">Ve výchozím nastavení pomocník značek `<form>` odesílá data formuláře pomocí příspěvku, což znamená, že parametry jsou předány v těle zprávy HTTP a nejsou v adrese URL jako řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="d7bfd-188">Když zadáte příkaz HTTP GET, data formuláře se předávají v adrese URL jako řetězce dotazů, které uživatelům umožňují záložku URL.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="d7bfd-189">Pokyny pro konsorcium W3C doporučují použít GET, když akce nevede k aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="d7bfd-190">Spusťte aplikaci, vyberte kartu **Students** , zadejte hledaný řetězec a kliknutím na Hledat ověřte, že filtrování funguje.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Stránka indexu studentů s filtrováním](sort-filter-page/_static/filtering.png)

<span data-ttu-id="d7bfd-192">Všimněte si, že adresa URL obsahuje hledaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="d7bfd-193">Pokud tuto stránku zařadíte do záložky, zobrazí se při použití záložky filtrovaný seznam.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="d7bfd-194">Přidání `method="get"` do značky `form` způsobilo vygenerování řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="d7bfd-195">Pokud v této fázi kliknete na odkaz seřadit záhlaví sloupce, ztratíte hodnotu filtru, kterou jste zadali do **vyhledávacího** pole.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="d7bfd-196">Vyřešíte to v další části.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="d7bfd-197">Přidat stránkování do indexu studentů</span><span class="sxs-lookup"><span data-stu-id="d7bfd-197">Add paging to Students Index</span></span>

<span data-ttu-id="d7bfd-198">Chcete-li přidat stránkování na stránku indexu studentů, vytvoříte třídu `PaginatedList`, která používá příkazy `Skip` a `Take` k filtrování dat na serveru místo toho, aby se vždy načítat všechny řádky tabulky.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="d7bfd-199">Pak provedete další změny v metodě `Index` a přidáte do zobrazení `Index` tlačítka pro stránkování.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="d7bfd-200">Následující ilustrace znázorňuje stránkování tlačítek.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-200">The following illustration shows the paging buttons.</span></span>

![Stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging.png)

<span data-ttu-id="d7bfd-202">Ve složce projektu vytvořte `PaginatedList.cs` a potom kód šablony nahraďte následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="d7bfd-203">Metoda `CreateAsync` v tomto kódu má velikost stránky a číslo stránky a aplikuje příslušné příkazy `Skip` a `Take` na `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="d7bfd-204">Když je na `IQueryable` volána hodnota `ToListAsync`, vrátí seznam obsahující pouze požadovanou stránku.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="d7bfd-205">Vlastnosti `HasPreviousPage` a `HasNextPage` lze použít k povolení nebo zakázání tlačítek **předchozí** a **Další** stránkování.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="d7bfd-206">Namísto konstruktoru pro vytvoření objektu `PaginatedList<T>` se používá metoda `CreateAsync`, protože konstruktory nemůžou spouštět asynchronní kód.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="d7bfd-207">Přidat stránkování do metody indexu</span><span class="sxs-lookup"><span data-stu-id="d7bfd-207">Add paging to Index method</span></span>

<span data-ttu-id="d7bfd-208">V *StudentsController.cs*nahraďte metodu `Index` následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="d7bfd-209">Tento kód přidá parametr čísla stránky, aktuální parametr pořadí řazení a aktuální parametr filtru na podpis metody.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="d7bfd-210">Při prvním zobrazení stránky, nebo pokud uživatel neklikl na odkaz na stránkování nebo řazení, všechny parametry budou mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="d7bfd-211">Pokud se klikne na odkaz na stránkování, proměnná stránky bude obsahovat číslo stránky, které se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="d7bfd-212">Element `ViewData` s názvem CurrentSort poskytuje zobrazení s aktuálním pořadím řazení, protože musí být součástí odkazů stránkování, aby bylo řazení stejné při stránkování.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="d7bfd-213">Element `ViewData` s názvem CurrentFilter poskytuje zobrazení s aktuálním řetězcem filtru.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="d7bfd-214">Tato hodnota musí být součástí odkazů stránkování, aby bylo možné zachovat nastavení filtru během stránkování, a při zobrazení stránky musí být obnovena do textového pole.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="d7bfd-215">Pokud se hledaný řetězec během stránkování změní, je nutné obnovit stránku na 1, protože nový filtr může mít za následek zobrazení různých dat.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="d7bfd-216">Hledaný řetězec se změní, když je v textovém poli vložena hodnota a stisknete tlačítko Odeslat.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="d7bfd-217">V takovém případě parametr `searchString` nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="d7bfd-218">Na konci metody `Index` převede metoda `PaginatedList.CreateAsync` dotaz student na jednu stránku studentů v typu kolekce, který podporuje stránkování.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="d7bfd-219">Tato jediná strana studentů se pak předává do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="d7bfd-220">Metoda `PaginatedList.CreateAsync` přebírá číslo stránky.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="d7bfd-221">Dvě otazníky reprezentují operátor slučování s hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="d7bfd-222">Operátor slučování null definuje výchozí hodnotu pro typ s možnou hodnotou null. výraz `(pageNumber ?? 1)` znamená, že hodnota `pageNumber` se vrátí, pokud má hodnotu, nebo vrátí hodnotu 1, pokud `pageNumber` je null.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="d7bfd-223">Přidat odkazy na stránkování</span><span class="sxs-lookup"><span data-stu-id="d7bfd-223">Add paging links</span></span>

<span data-ttu-id="d7bfd-224">V *zobrazeních/Students/index. cshtml*nahraďte existující kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="d7bfd-225">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="d7bfd-226">Příkaz `@model` v horní části stránky určuje, že zobrazení nyní Získá objekt `PaginatedList<T>` namísto objektu `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="d7bfd-227">Záhlaví sloupce odkazuje pomocí řetězce dotazu k předání aktuálního vyhledávacího řetězce k řadiči, aby uživatel mohl seřadit výsledky filtru:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="d7bfd-228">Tlačítka pro stránkování se zobrazují v pomocníkech značek:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="d7bfd-229">Spusťte aplikaci a pokračujte na stránku students.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-229">Run the app and go to the Students page.</span></span>

![Stránka indexu studentů s odkazy na stránkování](sort-filter-page/_static/paging.png)

<span data-ttu-id="d7bfd-231">Kliknutím na odkazy na stránkování v různých pořadích řazení zajistěte, aby stránkování fungovalo.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="d7bfd-232">Pak zadejte hledaný řetězec a zkuste znovu vytvořit stránkování, abyste ověřili, že stránkování funguje i správně s řazením a filtrováním.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="d7bfd-233">Vytvoření stránky o stránku</span><span class="sxs-lookup"><span data-stu-id="d7bfd-233">Create an About page</span></span>

<span data-ttu-id="d7bfd-234">Pro **stránku se stránkou společnosti Contoso** na univerzitě se zobrazí, kolik studentů se zaregistrovalo pro každé datum registrace.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="d7bfd-235">To vyžaduje seskupování a jednoduché výpočty skupin.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="d7bfd-236">K tomu je třeba provést následující akce:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="d7bfd-237">Vytvořte třídu zobrazení modelu pro data, která potřebujete předat zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="d7bfd-238">Vytvořte v domovském řadiči metodu About.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="d7bfd-239">Vytvořte zobrazení informace o.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="d7bfd-240">Vytvoření modelu zobrazení</span><span class="sxs-lookup"><span data-stu-id="d7bfd-240">Create the view model</span></span>

<span data-ttu-id="d7bfd-241">Vytvořte složku *SchoolViewModels* ve složce *modely* .</span><span class="sxs-lookup"><span data-stu-id="d7bfd-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="d7bfd-242">V nové složce přidejte soubor třídy *EnrollmentDateGroup.cs* a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="d7bfd-243">Úprava domovského kontroleru</span><span class="sxs-lookup"><span data-stu-id="d7bfd-243">Modify the Home Controller</span></span>

<span data-ttu-id="d7bfd-244">V *HomeController.cs*přidejte do horní části souboru následující příkazy using:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="d7bfd-245">Přidejte proměnnou třídy pro kontext databáze hned za levou složenou závorku pro třídu a Získejte instanci kontextu z ASP.NET Core DI:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="d7bfd-246">Přidejte metodu `About` s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="d7bfd-247">Příkaz LINQ seskupuje entity studenta podle data registrace, vypočítá počet entit v každé skupině a uloží výsledky do kolekce objektů zobrazení `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="d7bfd-248">Vytvoření zobrazení o zobrazení</span><span class="sxs-lookup"><span data-stu-id="d7bfd-248">Create the About View</span></span>

<span data-ttu-id="d7bfd-249">Přidejte *zobrazení/domů/o soubor. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="d7bfd-250">Spusťte aplikaci a pokračujte na stránku o produktu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-250">Run the app and go to the About page.</span></span> <span data-ttu-id="d7bfd-251">V tabulce se zobrazí počet studentů pro každé datum zápisu.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="d7bfd-252">Získat kód</span><span class="sxs-lookup"><span data-stu-id="d7bfd-252">Get the code</span></span>

[<span data-ttu-id="d7bfd-253">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-253">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="d7bfd-254">Další kroky</span><span class="sxs-lookup"><span data-stu-id="d7bfd-254">Next steps</span></span>

<span data-ttu-id="d7bfd-255">V tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="d7bfd-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d7bfd-256">Přidané odkazy na řazení sloupců</span><span class="sxs-lookup"><span data-stu-id="d7bfd-256">Added column sort links</span></span>
> * <span data-ttu-id="d7bfd-257">Přidání vyhledávacího pole</span><span class="sxs-lookup"><span data-stu-id="d7bfd-257">Added a Search box</span></span>
> * <span data-ttu-id="d7bfd-258">Přidání stránkování do indexu studentů</span><span class="sxs-lookup"><span data-stu-id="d7bfd-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="d7bfd-259">Přidání stránkování do metody indexu</span><span class="sxs-lookup"><span data-stu-id="d7bfd-259">Added paging to Index method</span></span>
> * <span data-ttu-id="d7bfd-260">Přidání odkazů na stránkování</span><span class="sxs-lookup"><span data-stu-id="d7bfd-260">Added paging links</span></span>
> * <span data-ttu-id="d7bfd-261">Vytvoření stránky o stránku</span><span class="sxs-lookup"><span data-stu-id="d7bfd-261">Created an About page</span></span>

<span data-ttu-id="d7bfd-262">Přejděte k dalšímu kurzu, kde se dozvíte, jak zpracovávat změny datového modelu pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="d7bfd-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d7bfd-263">Další: zpracování změn datového modelu</span><span class="sxs-lookup"><span data-stu-id="d7bfd-263">Next: Handle data model changes</span></span>](migrations.md)
