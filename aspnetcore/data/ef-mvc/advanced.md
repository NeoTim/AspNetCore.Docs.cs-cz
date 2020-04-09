---
title: 'Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core'
description: Tento kurz představuje užitečná témata pro překročení základy vývoje ASP.NET základní webové aplikace, které používají Entity Framework Core.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: fc6f8d8c4ab09848cf316be2e522bf5ce3b9ac76
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416231"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="ca037-103">Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="ca037-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="ca037-104">V předchozím kurzu jste implementovali dědičnost tabulky na hierarchii.</span><span class="sxs-lookup"><span data-stu-id="ca037-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="ca037-105">Tento kurz představuje několik témat, která jsou užitečná, abyste si byli vědomi, když překročíte základy vývoje ASP.NET základní webové aplikace, které používají core entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="ca037-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="ca037-106">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="ca037-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ca037-107">Provádění nezpracovaných dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="ca037-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="ca037-108">Volání dotazu k vrácení entit</span><span class="sxs-lookup"><span data-stu-id="ca037-108">Call a query to return entities</span></span>
> * <span data-ttu-id="ca037-109">Volání dotazu pro vrácení jiných typů</span><span class="sxs-lookup"><span data-stu-id="ca037-109">Call a query to return other types</span></span>
> * <span data-ttu-id="ca037-110">Volání aktualizačního dotazu</span><span class="sxs-lookup"><span data-stu-id="ca037-110">Call an update query</span></span>
> * <span data-ttu-id="ca037-111">Prozkoumání dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="ca037-111">Examine SQL queries</span></span>
> * <span data-ttu-id="ca037-112">Vytvoření vrstvy abstrakce</span><span class="sxs-lookup"><span data-stu-id="ca037-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="ca037-113">Další informace o automatickém zjišťování změn</span><span class="sxs-lookup"><span data-stu-id="ca037-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="ca037-114">Informace o zdrojovém kódu EF Core a plánech rozvoje</span><span class="sxs-lookup"><span data-stu-id="ca037-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="ca037-115">Naučte se používat dynamické LINQ ke zjednodušení kódu</span><span class="sxs-lookup"><span data-stu-id="ca037-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ca037-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ca037-116">Prerequisites</span></span>

* [<span data-ttu-id="ca037-117">Implementovat dědičnost</span><span class="sxs-lookup"><span data-stu-id="ca037-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="ca037-118">Provádění nezpracovaných dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="ca037-118">Perform raw SQL queries</span></span>

<span data-ttu-id="ca037-119">Jednou z výhod použití entity framework je, že se zabrání vázání kódu příliš úzce na konkrétní metodu ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="ca037-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="ca037-120">Je to tím, že generuje SQL dotazy a příkazy pro vás, což také osvobozuje od nutnosti psát sami.</span><span class="sxs-lookup"><span data-stu-id="ca037-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="ca037-121">Existují však výjimečné scénáře, kdy potřebujete spustit konkrétní dotazy SQL, které jste vytvořili ručně.</span><span class="sxs-lookup"><span data-stu-id="ca037-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="ca037-122">Pro tyto scénáře entity framework code first api obsahuje metody, které umožňují předat příkazy SQL přímo do databáze.</span><span class="sxs-lookup"><span data-stu-id="ca037-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="ca037-123">V EF Core 1.0 máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="ca037-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="ca037-124">Metodu `DbSet.FromSql` použijte pro dotazy, které vracejí typy entit.</span><span class="sxs-lookup"><span data-stu-id="ca037-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="ca037-125">Vrácené objekty musí být typu `DbSet` očekávaného objektem a jsou automaticky sledovány kontextem databáze, pokud sledování [nevypnete](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="ca037-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="ca037-126">Použijte `Database.ExecuteSqlCommand` pro příkazy bez dotazu.</span><span class="sxs-lookup"><span data-stu-id="ca037-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="ca037-127">Pokud potřebujete spustit dotaz, který vrací typy, které nejsou entity, můžete použít ADO.NET s připojením k databázi poskytované EF.</span><span class="sxs-lookup"><span data-stu-id="ca037-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="ca037-128">Vrácená data nejsou sledována kontextem databáze, i když tuto metodu použijete k načtení typů entit.</span><span class="sxs-lookup"><span data-stu-id="ca037-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="ca037-129">Jak je vždy pravda při spuštění příkazů SQL ve webové aplikaci, je nutné přijmout opatření k ochraně webu před útoky injektáže SQL.</span><span class="sxs-lookup"><span data-stu-id="ca037-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="ca037-130">Jedním ze způsobů, jak to udělat, je použít parametrizované dotazy a ujistěte se, že řetězce odeslané webovou stránkou nelze interpretovat jako příkazy SQL.</span><span class="sxs-lookup"><span data-stu-id="ca037-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="ca037-131">V tomto kurzu budete používat parametrizované dotazy při integraci vstupu uživatele do dotazu.</span><span class="sxs-lookup"><span data-stu-id="ca037-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="ca037-132">Volání dotazu k vrácení entit</span><span class="sxs-lookup"><span data-stu-id="ca037-132">Call a query to return entities</span></span>

<span data-ttu-id="ca037-133">Třída `DbSet<TEntity>` poskytuje metodu, kterou můžete použít ke spuštění dotazu, který vrací entitu typu `TEntity`.</span><span class="sxs-lookup"><span data-stu-id="ca037-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="ca037-134">Chcete-li zjistit, jak to funguje, `Details` změníte kód v metodě řadiče oddělení.</span><span class="sxs-lookup"><span data-stu-id="ca037-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="ca037-135">V *DepartmentsController.cs*v `Details` metodě nahraďte kód, který `FromSql` načítá oddělení voláním metody, jak je znázorněno v následujícím zvýrazněném kódu:</span><span class="sxs-lookup"><span data-stu-id="ca037-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="ca037-136">Chcete-li ověřit, zda nový kód funguje správně, vyberte kartu **Oddělení** a potom **podrobnosti** pro jedno z oddělení.</span><span class="sxs-lookup"><span data-stu-id="ca037-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Podrobnosti o oddělení](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="ca037-138">Volání dotazu pro vrácení jiných typů</span><span class="sxs-lookup"><span data-stu-id="ca037-138">Call a query to return other types</span></span>

<span data-ttu-id="ca037-139">Dříve jste pro stránku Informace vytvořili mřížku statistik studentů, která zobrazuje počet studentů pro každé datum zápisu.</span><span class="sxs-lookup"><span data-stu-id="ca037-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="ca037-140">Získali jste data ze sady`_context.Students`entit Studenty ( ) a `EnrollmentDateGroup` použili jste LINQ k promítání výsledků do seznamu objektů modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ca037-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="ca037-141">Předpokládejme, že chcete napsat sql sám spíše než pomocí LINQ.</span><span class="sxs-lookup"><span data-stu-id="ca037-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="ca037-142">Chcete-li to provést, je třeba spustit dotaz SQL, který vrací něco jiného než objekty entity.</span><span class="sxs-lookup"><span data-stu-id="ca037-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="ca037-143">V EF Core 1.0 jedním ze způsobů, jak to udělat, je napsat ADO.NET kódu a získat připojení k databázi z EF.</span><span class="sxs-lookup"><span data-stu-id="ca037-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="ca037-144">V *HomeController.cs*nahraďte metodu `About` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ca037-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="ca037-145">Přidejte příkaz using:</span><span class="sxs-lookup"><span data-stu-id="ca037-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="ca037-146">Spusťte aplikaci a přejděte na stránku Informace.</span><span class="sxs-lookup"><span data-stu-id="ca037-146">Run the app and go to the About page.</span></span> <span data-ttu-id="ca037-147">Zobrazuje stejná data jako předtím.</span><span class="sxs-lookup"><span data-stu-id="ca037-147">It displays the same data it did before.</span></span>

![O stránce](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="ca037-149">Volání aktualizačního dotazu</span><span class="sxs-lookup"><span data-stu-id="ca037-149">Call an update query</span></span>

<span data-ttu-id="ca037-150">Předpokládejme, že správci Univerzity Contoso chtějí provádět globální změny v databázi, například změnu počtu kreditů pro každý kurz.</span><span class="sxs-lookup"><span data-stu-id="ca037-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="ca037-151">V případě, že univerzita má velký počet kurzů, bylo by neefektivní získat je všechny jako subjekty a změnit je jednotlivě.</span><span class="sxs-lookup"><span data-stu-id="ca037-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="ca037-152">V této části budete implementovat webovou stránku, která umožňuje uživateli určit faktor, kterým chcete změnit počet kreditů pro všechny kurzy a provedete změnu spuštěním příkazu SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="ca037-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="ca037-153">Webová stránka bude vypadat na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="ca037-153">The web page will look like the following illustration:</span></span>

![Aktualizovat stránku Kreditů kurzu](advanced/_static/update-credits.png)

<span data-ttu-id="ca037-155">V *CoursesController.cs*přidejte metody UpdateCourseCredits pro HttpGet a HttpPost:</span><span class="sxs-lookup"><span data-stu-id="ca037-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="ca037-156">Když řadič zpracuje požadavek HttpGet, `ViewData["RowsAffected"]`nic se vrátí v aplikaci a zobrazení zobrazí prázdné textové pole a tlačítko odeslat, jak je znázorněno na předchozím obrázku.</span><span class="sxs-lookup"><span data-stu-id="ca037-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="ca037-157">Po klepnutí na tlačítko **Aktualizovat** je volána metoda HttpPost a multiplikátor má hodnotu zadanou v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="ca037-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="ca037-158">Kód pak spustí SQL, který aktualizuje kurzy a vrátí počet `ViewData`ovlivněných řádků do zobrazení v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="ca037-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="ca037-159">Když zobrazení získá `RowsAffected` hodnotu, zobrazí počet aktualizovaných řádků.</span><span class="sxs-lookup"><span data-stu-id="ca037-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="ca037-160">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Zobrazení nebo Kurzy* a potom klepněte na příkaz **Přidat > novou položku**.</span><span class="sxs-lookup"><span data-stu-id="ca037-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="ca037-161">V dialogovém okně **Přidat novou položku** klepněte na **ASP.NET jádro** v části **Nainstalováno** v levém podokně, klepněte na **položku Razor View**a pojmenujte nové zobrazení *UpdateCourseCredits.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="ca037-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="ca037-162">V *části Zobrazení/Kurzy/UpdateCourseCredits.cshtml*nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ca037-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="ca037-163">Spusťte metodu `UpdateCourseCredits` výběrem karty **Kurzy** a poté přidáte "/UpdateCourseCredits" na konec adresy URL `http://localhost:5813/Courses/UpdateCourseCredits`v adresním řádku prohlížeče (například: ).</span><span class="sxs-lookup"><span data-stu-id="ca037-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="ca037-164">Do textového pole zadejte číslo:</span><span class="sxs-lookup"><span data-stu-id="ca037-164">Enter a number in the text box:</span></span>

![Aktualizovat stránku Kreditů kurzu](advanced/_static/update-credits.png)

<span data-ttu-id="ca037-166">Klikněte na **Aktualizovat**.</span><span class="sxs-lookup"><span data-stu-id="ca037-166">Click **Update**.</span></span> <span data-ttu-id="ca037-167">Zobrazí se počet ovlivněných řádků:</span><span class="sxs-lookup"><span data-stu-id="ca037-167">You see the number of rows affected:</span></span>

![Aktualizace ovlivněných řádků stránky Kredity kurzu](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="ca037-169">Kliknutím na **Zpět do seznamu** zobrazíte seznam kurzů s revidovaným počtem kreditů.</span><span class="sxs-lookup"><span data-stu-id="ca037-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="ca037-170">Všimněte si, že produkční kód by zajistil, že aktualizace vždy za následek platná data.</span><span class="sxs-lookup"><span data-stu-id="ca037-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="ca037-171">Zde uvedený zjednodušený kód by mohl znásobit počet kreditů natolik, aby vedl k číslům větším než 5.</span><span class="sxs-lookup"><span data-stu-id="ca037-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="ca037-172">(Vlastnost `Credits` má `[Range(0, 5)]` atribut.) Aktualizační dotaz by fungoval, ale neplatná data by mohla způsobit neočekávané výsledky v jiných částech systému, které předpokládají, že počet kreditů je 5 nebo méně.</span><span class="sxs-lookup"><span data-stu-id="ca037-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="ca037-173">Další informace o nezpracovaných dotazech SQL naleznete [v tématu Raw SQL Queries](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="ca037-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="ca037-174">Prozkoumání dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="ca037-174">Examine SQL queries</span></span>

<span data-ttu-id="ca037-175">Někdy je užitečné mít možnost zobrazit skutečné dotazy SQL, které jsou odesílány do databáze.</span><span class="sxs-lookup"><span data-stu-id="ca037-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="ca037-176">Vestavěné funkce protokolování pro ASP.NET Core se automaticky používá EF Core k zápisu protokolů, které obsahují SQL pro dotazy a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="ca037-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="ca037-177">V této části uvidíte několik příkladů protokolování SQL.</span><span class="sxs-lookup"><span data-stu-id="ca037-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="ca037-178">Otevřete *StudentsController.cs* `Details` a v metodě `if (student == null)` nastavte zarážku na příkazu.</span><span class="sxs-lookup"><span data-stu-id="ca037-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="ca037-179">Spusťte aplikaci v režimu ladění a přejděte na stránku Podrobnosti pro studenta.</span><span class="sxs-lookup"><span data-stu-id="ca037-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="ca037-180">Přejděte do okna **Výstup** zobrazující výstup ladění a zobrazí se dotaz:</span><span class="sxs-lookup"><span data-stu-id="ca037-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="ca037-181">Zde si všimnete něčeho, co vás může překvapit:`TOP(2)`SQL vybere až 2 řádky ( ) z tabulky Osoba.</span><span class="sxs-lookup"><span data-stu-id="ca037-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="ca037-182">Metoda `SingleOrDefaultAsync` není přeložit na 1 řádek na serveru.</span><span class="sxs-lookup"><span data-stu-id="ca037-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="ca037-183">Důvod:</span><span class="sxs-lookup"><span data-stu-id="ca037-183">Here's why:</span></span>

* <span data-ttu-id="ca037-184">Pokud dotaz vrátí více řádků, metoda vrátí null.</span><span class="sxs-lookup"><span data-stu-id="ca037-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="ca037-185">Chcete-li zjistit, zda dotaz vrátí více řádků, EF musí zkontrolovat, zda vrátí alespoň 2.</span><span class="sxs-lookup"><span data-stu-id="ca037-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="ca037-186">Všimněte si, že není třeba použít režim ladění a zastavit na zarážky získat výstup protokolování v okně **Výstup.**</span><span class="sxs-lookup"><span data-stu-id="ca037-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="ca037-187">Je to jen pohodlný způsob, jak zastavit protokolování v okamžiku, kdy se chcete podívat na výstup.</span><span class="sxs-lookup"><span data-stu-id="ca037-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="ca037-188">Pokud tak neučiníte, protokolování pokračuje a budete muset posunout zpět najít části, které vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="ca037-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="ca037-189">Vytvoření vrstvy abstrakce</span><span class="sxs-lookup"><span data-stu-id="ca037-189">Create an abstraction layer</span></span>

<span data-ttu-id="ca037-190">Mnoho vývojářů psát kód k implementaci úložiště a jednotky pracovních vzorů jako obálku kolem kódu, který pracuje s entity framework.</span><span class="sxs-lookup"><span data-stu-id="ca037-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="ca037-191">Tyto vzory jsou určeny k vytvoření vrstvy abstrakce mezi vrstvou přístupu k datům a vrstvou obchodní logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="ca037-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="ca037-192">Implementace těchto vzorů může pomoci izolovat vaši aplikaci před změnami v úložišti dat a může usnadnit automatizované testování částí nebo vývoj řízený testováním (TDD).</span><span class="sxs-lookup"><span data-stu-id="ca037-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="ca037-193">Psaní dalšího kódu k implementaci těchto vzorů však není vždy nejlepší volbou pro aplikace, které používají EF, z několika důvodů:</span><span class="sxs-lookup"><span data-stu-id="ca037-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="ca037-194">Třída kontextu EF sama o sobě izoluje váš kód z kódu specifické pro úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="ca037-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="ca037-195">Třída kontextu EF může fungovat jako jednotka práce třídy pro aktualizace databáze, které používáte EF.</span><span class="sxs-lookup"><span data-stu-id="ca037-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="ca037-196">EF obsahuje funkce pro implementaci TDD bez psaní kódu úložiště.</span><span class="sxs-lookup"><span data-stu-id="ca037-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="ca037-197">Informace o tom, jak implementovat úložiště a jednotky pracovních vzorů, naleznete [v entity Framework 5 verze této série kurzů](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="ca037-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="ca037-198">Entity Framework Core implementuje poskytovatele databáze v paměti, který lze použít pro testování.</span><span class="sxs-lookup"><span data-stu-id="ca037-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="ca037-199">Další informace naleznete v [tématu Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="ca037-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="ca037-200">Automatická detekce změn</span><span class="sxs-lookup"><span data-stu-id="ca037-200">Automatic change detection</span></span>

<span data-ttu-id="ca037-201">Entity Framework určuje, jak se účetní jednotka změnila (a proto je třeba odeslat aktualizace do databáze) porovnáním aktuálních hodnot entity s původními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="ca037-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="ca037-202">Původní hodnoty jsou uloženy při dotazování nebo připojení entity.</span><span class="sxs-lookup"><span data-stu-id="ca037-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="ca037-203">Některé metody, které způsobují automatické zjišťování změn, jsou následující:</span><span class="sxs-lookup"><span data-stu-id="ca037-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="ca037-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="ca037-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="ca037-205">DbContext.Položka</span><span class="sxs-lookup"><span data-stu-id="ca037-205">DbContext.Entry</span></span>

* <span data-ttu-id="ca037-206">ChangeTracker.Položky</span><span class="sxs-lookup"><span data-stu-id="ca037-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="ca037-207">Pokud sledujete velký počet entit a voláte jednu z těchto metod mnohokrát ve smyčce, můžete získat významné `ChangeTracker.AutoDetectChangesEnabled` zlepšení výkonu dočasným vypnutím automatického zjišťování změn pomocí vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="ca037-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="ca037-208">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ca037-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="ca037-209">EF Základní zdrojový kód a plány rozvoje</span><span class="sxs-lookup"><span data-stu-id="ca037-209">EF Core source code and development plans</span></span>

<span data-ttu-id="ca037-210">Zdroj core entity framework [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)je na .</span><span class="sxs-lookup"><span data-stu-id="ca037-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="ca037-211">Úložiště EF Core obsahuje noční sestavení, sledování problémů, specifikace funkcí, poznámky k návrhu schůzky a [plán budoucího vývoje](https://github.com/dotnet/efcore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="ca037-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="ca037-212">Můžete soubor nebo najít chyby, a přispět.</span><span class="sxs-lookup"><span data-stu-id="ca037-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="ca037-213">Přestože zdrojový kód je otevřený, Entity Framework Core je plně podporován jako produkt společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="ca037-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="ca037-214">Tým Microsoft Entity Framework udržuje kontrolu nad příspěvky, které jsou přijímány a testuje všechny změny kódu, aby byla zajištěna kvalita každé verze.</span><span class="sxs-lookup"><span data-stu-id="ca037-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="ca037-215">Zpětná inženýrská instrukace z existující databáze</span><span class="sxs-lookup"><span data-stu-id="ca037-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="ca037-216">Chcete-li zpětně analyzovat datový model včetně tříd entit z existující databáze, použijte příkaz [scaffold-dbcontext.](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext)</span><span class="sxs-lookup"><span data-stu-id="ca037-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="ca037-217">Podívejte se na [výuku začínáme](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="ca037-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="ca037-218">Zjednodušení kódu pomocí dynamického LINQ</span><span class="sxs-lookup"><span data-stu-id="ca037-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="ca037-219">[Třetí kurz v této sérii](sort-filter-page.md) ukazuje, jak psát kód LINQ `switch` pomocí pevně kódující názvy sloupců v příkazu.</span><span class="sxs-lookup"><span data-stu-id="ca037-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="ca037-220">Se dvěma sloupci na výběr, to funguje dobře, ale pokud máte mnoho sloupců kód může získat podrobné.</span><span class="sxs-lookup"><span data-stu-id="ca037-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="ca037-221">Chcete-li tento problém vyřešit, můžete použít metodu `EF.Property` k určení názvu vlastnosti jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="ca037-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="ca037-222">Chcete-li vyzkoušet tento `Index` přístup, `StudentsController` nahraďte metodu v následujícím kódu.</span><span class="sxs-lookup"><span data-stu-id="ca037-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="ca037-223">Poděkování</span><span class="sxs-lookup"><span data-stu-id="ca037-223">Acknowledgments</span></span>

<span data-ttu-id="ca037-224">Tom Dykstra a Rick @RickAndMSFTAnderson (twitter) napsal tento výukový program.</span><span class="sxs-lookup"><span data-stu-id="ca037-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="ca037-225">Rowan Miller, Diego Vega a další členové týmu Entity Framework pomáhali s revizemi kódu a pomáhali ladit problémy, které vznikly při psaní kódu pro kurzy.</span><span class="sxs-lookup"><span data-stu-id="ca037-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="ca037-226">John Parente a Paul Goldman pracovali na aktualizaci kurzu pro ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="ca037-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="ca037-227">Odstraňování běžných chyb</span><span class="sxs-lookup"><span data-stu-id="ca037-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="ca037-228">ContosoUniversity.dll používá jiný proces</span><span class="sxs-lookup"><span data-stu-id="ca037-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="ca037-229">Chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="ca037-229">Error message:</span></span>

> <span data-ttu-id="ca037-230">Nelze otevřít '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' pro zápis – Proces nemůže získat přístup k souboru ...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll', protože je používán jiným procesem.</span><span class="sxs-lookup"><span data-stu-id="ca037-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="ca037-231">Řešení:</span><span class="sxs-lookup"><span data-stu-id="ca037-231">Solution:</span></span>

<span data-ttu-id="ca037-232">Zastavte web ve správě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ca037-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="ca037-233">Přejděte na panel windows, najděte službu IIS Express a klepněte pravým tlačítkem myši na její ikonu, vyberte web Univerzity Contoso a klepněte na příkaz **Zastavit web**.</span><span class="sxs-lookup"><span data-stu-id="ca037-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="ca037-234">Migrace šavle bez kódu v metodách Nahoru a Dolů</span><span class="sxs-lookup"><span data-stu-id="ca037-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="ca037-235">Možná příčina:</span><span class="sxs-lookup"><span data-stu-id="ca037-235">Possible cause:</span></span>

<span data-ttu-id="ca037-236">Příkazy příkazového příkazu PŘÍKAZEf automaticky neuzavírají a neukládají soubory kódu.</span><span class="sxs-lookup"><span data-stu-id="ca037-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="ca037-237">Pokud máte neuložené změny při `migrations add` spuštění příkazu, EF nenajde změny.</span><span class="sxs-lookup"><span data-stu-id="ca037-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="ca037-238">Řešení:</span><span class="sxs-lookup"><span data-stu-id="ca037-238">Solution:</span></span>

<span data-ttu-id="ca037-239">Spusťte `migrations remove` příkaz, uložte změny `migrations add` kódu a znovu spusťte příkaz.</span><span class="sxs-lookup"><span data-stu-id="ca037-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="ca037-240">Chyby při spuštění aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="ca037-240">Errors while running database update</span></span>

<span data-ttu-id="ca037-241">Je možné získat další chyby při provádění změn schématu v databázi, která má existující data.</span><span class="sxs-lookup"><span data-stu-id="ca037-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="ca037-242">Pokud se vám budou chyby migrace vyřešit, můžete změnit název databáze v připojovacím řetězci nebo databázi odstranit.</span><span class="sxs-lookup"><span data-stu-id="ca037-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="ca037-243">S novou databází nejsou k dispozici žádná data k migraci a příkaz databáze aktualizací je mnohem pravděpodobnější, že bude dokončen bez chyb.</span><span class="sxs-lookup"><span data-stu-id="ca037-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="ca037-244">Nejjednodušší přístup je přejmenovat databázi v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ca037-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="ca037-245">Při příštím spuštění `database update`bude vytvořena nová databáze.</span><span class="sxs-lookup"><span data-stu-id="ca037-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="ca037-246">Chcete-li odstranit databázi ve ssoxu, klepněte pravým tlačítkem myši na databázi, klepněte na příkaz **Odstranit**a potom v dialogovém okně **Odstranit databázi** vyberte **zavřít existující připojení** a klepněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="ca037-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="ca037-247">Chcete-li odstranit databázi pomocí příkazového příkazu příkazového příkazu, spusťte příkaz příkazu příkazu příkazu k příkazu: `database drop`</span><span class="sxs-lookup"><span data-stu-id="ca037-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="ca037-248">Při vyhledání instance serveru SQL Server došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="ca037-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="ca037-249">Chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="ca037-249">Error Message:</span></span>

> <span data-ttu-id="ca037-250">Při navazování připojení k SQL Serveru došlo k chybě související se sítí nebo konkrétní instancí.</span><span class="sxs-lookup"><span data-stu-id="ca037-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="ca037-251">Server se nenašel nebo nebyl dostupný.</span><span class="sxs-lookup"><span data-stu-id="ca037-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="ca037-252">Ověřte, zda je název instance správný a zda je server SQL Server nakonfigurován tak, aby umožňoval vzdálená připojení.</span><span class="sxs-lookup"><span data-stu-id="ca037-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="ca037-253">(poskytovatel: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span><span class="sxs-lookup"><span data-stu-id="ca037-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="ca037-254">Řešení:</span><span class="sxs-lookup"><span data-stu-id="ca037-254">Solution:</span></span>

<span data-ttu-id="ca037-255">Zkontrolujte připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="ca037-255">Check the connection string.</span></span> <span data-ttu-id="ca037-256">Pokud jste ručně odstranili soubor databáze, změňte název databáze ve stavebním řetězci a začněte znovu s novou databází.</span><span class="sxs-lookup"><span data-stu-id="ca037-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="ca037-257">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="ca037-257">Get the code</span></span>

[<span data-ttu-id="ca037-258">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ca037-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="ca037-259">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ca037-259">Additional resources</span></span>

<span data-ttu-id="ca037-260">Další informace o ef core naleznete [v dokumentaci core entity frameworku](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="ca037-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="ca037-261">Kniha je také k dispozici: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="ca037-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="ca037-262">Informace o nasazení webové aplikace naleznete <xref:host-and-deploy/index>v tématu .</span><span class="sxs-lookup"><span data-stu-id="ca037-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="ca037-263">Informace o dalších tématech souvisejících s ASP.NET core MVC, například ověřování a autorizace, naleznete v tématu <xref:index>.</span><span class="sxs-lookup"><span data-stu-id="ca037-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ca037-264">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ca037-264">Next steps</span></span>

<span data-ttu-id="ca037-265">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="ca037-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ca037-266">Provedené nezpracované dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="ca037-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="ca037-267">Nazývá se dotaz na vrácení entit</span><span class="sxs-lookup"><span data-stu-id="ca037-267">Called a query to return entities</span></span>
> * <span data-ttu-id="ca037-268">Volal dotaz vrátit jiné typy</span><span class="sxs-lookup"><span data-stu-id="ca037-268">Called a query to return other types</span></span>
> * <span data-ttu-id="ca037-269">Nazývá se aktualizační dotaz</span><span class="sxs-lookup"><span data-stu-id="ca037-269">Called an update query</span></span>
> * <span data-ttu-id="ca037-270">Zkoumané dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="ca037-270">Examined SQL queries</span></span>
> * <span data-ttu-id="ca037-271">Vytvoření vrstvy abstrakce</span><span class="sxs-lookup"><span data-stu-id="ca037-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="ca037-272">Informace o automatickém zjišťování změn</span><span class="sxs-lookup"><span data-stu-id="ca037-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="ca037-273">Informace o zdrojovém kódu EF Core a plánech rozvoje</span><span class="sxs-lookup"><span data-stu-id="ca037-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="ca037-274">Naučili se používat dynamické LINQ ke zjednodušení kódu</span><span class="sxs-lookup"><span data-stu-id="ca037-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="ca037-275">Tím se dokončí tato řada kurzů o použití jádra entity frameworku v ASP.NET základní aplikace MVC.</span><span class="sxs-lookup"><span data-stu-id="ca037-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="ca037-276">Tato řada pracovala s novou databází; Alternativou je zpětná inženýrská organizace modelu z existující databáze.</span><span class="sxs-lookup"><span data-stu-id="ca037-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ca037-277">Kurz: EF Core s MVC, existující databáze</span><span class="sxs-lookup"><span data-stu-id="ca037-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
