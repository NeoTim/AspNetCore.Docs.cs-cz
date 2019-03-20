---
title: 'Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core'
description: Tento kurz představuje užitečné tématech překračují základní informace o vývoji webových aplikací ASP.NET Core využívající Entity Framework Core.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: c4804bd6614c7d5a2a30c8f59a645f603929ad52
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264585"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="95a3d-103">Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="95a3d-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="95a3d-104">V předchozím kurzu jste implementovali tabulky na hierarchii dědičnosti.</span><span class="sxs-lookup"><span data-stu-id="95a3d-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="95a3d-105">Tento kurz představuje několik témat, které jsou užitečné mít na paměti při nad rámec základní informace o vývoji webových aplikací ASP.NET Core využívající Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="95a3d-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="95a3d-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="95a3d-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="95a3d-107">Spouštějte nezpracované dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="95a3d-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="95a3d-108">Volání dotaz, který vrací entity</span><span class="sxs-lookup"><span data-stu-id="95a3d-108">Call a query to return entities</span></span>
> * <span data-ttu-id="95a3d-109">Volání dotaz, který jsou vraceny další typy</span><span class="sxs-lookup"><span data-stu-id="95a3d-109">Call a query to return other types</span></span>
> * <span data-ttu-id="95a3d-110">Volání dotaz update</span><span class="sxs-lookup"><span data-stu-id="95a3d-110">Call an update query</span></span>
> * <span data-ttu-id="95a3d-111">Zkontrolujte dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="95a3d-111">Examine SQL queries</span></span>
> * <span data-ttu-id="95a3d-112">Vytvořit vrstvu HAL</span><span class="sxs-lookup"><span data-stu-id="95a3d-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="95a3d-113">Další informace o zjišťování automaticky změnit</span><span class="sxs-lookup"><span data-stu-id="95a3d-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="95a3d-114">Další informace o plánech zdrojového kódu a vývoj EF Core</span><span class="sxs-lookup"><span data-stu-id="95a3d-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="95a3d-115">Další informace o použití dynamického LINQ pro zjednodušení kódu</span><span class="sxs-lookup"><span data-stu-id="95a3d-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="95a3d-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="95a3d-116">Prerequisites</span></span>

* [<span data-ttu-id="95a3d-117">Implementace dědičnosti s EF Core ve webové aplikaci ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="95a3d-117">Implement Inheritance with EF Core in an ASP.NET Core MVC web app</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="95a3d-118">Spouštějte nezpracované dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="95a3d-118">Perform raw SQL queries</span></span>

<span data-ttu-id="95a3d-119">Jednou z výhod používající nástroj Entity Framework je, že se eliminuje propojí váš kód příliš úzce na konkrétní metodu ukládat data.</span><span class="sxs-lookup"><span data-stu-id="95a3d-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="95a3d-120">Dělá to tak, že generování dotazy SQL a příkazy, které také díky které by bylo nutné napsat sami.</span><span class="sxs-lookup"><span data-stu-id="95a3d-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="95a3d-121">Ale existují výjimečné situace, kdy je potřeba spustit konkrétní dotazy SQL, které ručně vytvoříte.</span><span class="sxs-lookup"><span data-stu-id="95a3d-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="95a3d-122">Pro tyto scénáře prvního rozhraní API sady Entity Framework kód obsahuje metody, které vám umožní předat příkazů SQL přímo do databáze.</span><span class="sxs-lookup"><span data-stu-id="95a3d-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="95a3d-123">Máte následující možnosti v EF Core 1.0:</span><span class="sxs-lookup"><span data-stu-id="95a3d-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="95a3d-124">Použití `DbSet.FromSql` metodu pro dotazy vracející typy entit.</span><span class="sxs-lookup"><span data-stu-id="95a3d-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="95a3d-125">Vrácených objektů musí být typu očekává `DbSet` objekt a že jste automaticky sleduje provedené kontext databáze není-li je [vypnout sledování](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="95a3d-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="95a3d-126">Použití `Database.ExecuteSqlCommand` pro příkazy bez dotazů.</span><span class="sxs-lookup"><span data-stu-id="95a3d-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="95a3d-127">Pokud je potřeba spustit dotaz, který vrátí typy, které nejsou entity, můžete pomocí připojení k databázi poskytované EF ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="95a3d-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="95a3d-128">Vrácená data není sledována kontext databáze, i když používáte tuto metodu pro načtení typů entit.</span><span class="sxs-lookup"><span data-stu-id="95a3d-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="95a3d-129">Jak platí vždy při spuštění příkazů SQL ve webové aplikaci, je nutné provést opatření k ochraně před útoky prostřednictvím injektáže SQL serveru.</span><span class="sxs-lookup"><span data-stu-id="95a3d-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="95a3d-130">Jednou z možností, které se pomocí parametrizovaných dotazů se ujistěte, že řetězce odeslané na webové stránce se nedá interpretovat jako příkazy SQL.</span><span class="sxs-lookup"><span data-stu-id="95a3d-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="95a3d-131">V tomto kurzu použijete parametrizovaných dotazů při integraci uživatelský vstup do dotazu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="95a3d-132">Volání dotaz, který vrací entity</span><span class="sxs-lookup"><span data-stu-id="95a3d-132">Call a query to return entities</span></span>

<span data-ttu-id="95a3d-133">`DbSet<TEntity>` Třída poskytuje metodu, která můžete použít k provedení dotazu, který vrací entity typu `TEntity`.</span><span class="sxs-lookup"><span data-stu-id="95a3d-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="95a3d-134">Pokud chcete zobrazit, jak vám to funguje budete změnit kód v `Details` metody kontroleru oddělení.</span><span class="sxs-lookup"><span data-stu-id="95a3d-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="95a3d-135">V *DepartmentsController.cs*v `Details` metoda, nahraďte kód, který načte oddělení s `FromSql` volání metody, jak je znázorněno v následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="95a3d-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

<span data-ttu-id="95a3d-136">Chcete-li ověřit, že nový kód funguje správně, vyberte **oddělení** kartu a potom **podrobnosti** pro jeden z jako vodítko použijte oddělení.</span><span class="sxs-lookup"><span data-stu-id="95a3d-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Podrobnosti o oddělení](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="95a3d-138">Volání dotaz, který jsou vraceny další typy</span><span class="sxs-lookup"><span data-stu-id="95a3d-138">Call a query to return other types</span></span>

<span data-ttu-id="95a3d-139">Dříve jste vytvořili mřížky student statistiky o stránky, které jsme si ukázali, počet studentů pro každé datum registrace.</span><span class="sxs-lookup"><span data-stu-id="95a3d-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="95a3d-140">Jste získali data ze sady entit pro studenty (`_context.Students`) a použít k projekci výsledků do seznamu LINQ `EnrollmentDateGroup` zobrazit objekty modelu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="95a3d-141">Předpokládejme, že chcete napsat SQL samotné spíše než pomocí jazyka LINQ.</span><span class="sxs-lookup"><span data-stu-id="95a3d-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="95a3d-142">Chcete-li provést, je nutné spustit dotaz SQL, který vrací jinou hodnotu než objekty entity.</span><span class="sxs-lookup"><span data-stu-id="95a3d-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="95a3d-143">Jeden způsob, jak to udělat v EF Core 1.0, je psaní kódu rozhraní ADO.NET a získání připojení k databázi z EF.</span><span class="sxs-lookup"><span data-stu-id="95a3d-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="95a3d-144">V *HomeController.cs*, nahraďte `About` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="95a3d-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="95a3d-145">Přidat sadu pomocí příkazu:</span><span class="sxs-lookup"><span data-stu-id="95a3d-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="95a3d-146">Spusťte aplikaci a přejděte na stránku o.</span><span class="sxs-lookup"><span data-stu-id="95a3d-146">Run the app and go to the About page.</span></span> <span data-ttu-id="95a3d-147">Zobrazí se stejná data, která předtím.</span><span class="sxs-lookup"><span data-stu-id="95a3d-147">It displays the same data it did before.</span></span>

![O stránku](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="95a3d-149">Volání dotaz update</span><span class="sxs-lookup"><span data-stu-id="95a3d-149">Call an update query</span></span>

<span data-ttu-id="95a3d-150">Předpokládejme, že správce společnosti Contoso University chcete provést globálních změn v databázi, jako je například změna číslo kredity pro každý kurz.</span><span class="sxs-lookup"><span data-stu-id="95a3d-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="95a3d-151">Pokud univerzity má velký počet kurzů, bylo by neefektivní načíst vše jako entity a měnit je jednotlivě.</span><span class="sxs-lookup"><span data-stu-id="95a3d-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="95a3d-152">V této části budete implementovat webovou stránku, která umožňuje uživateli zadat faktor, podle kterého chcete změnit počet kredity pro všechny kurzy a provede změny spuštěním příkazu SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="95a3d-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="95a3d-153">Webové stránky bude vypadat jako na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="95a3d-153">The web page will look like the following illustration:</span></span>

![Stránka pro aktualizaci kurzu kredity](advanced/_static/update-credits.png)

<span data-ttu-id="95a3d-155">V *CoursesController.cs*, přidejte UpdateCourseCredits metody třídy MetadataExchangeClientMode a HttpPost:</span><span class="sxs-lookup"><span data-stu-id="95a3d-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="95a3d-156">Pokud kontroler zpracovává požadavek HttpGet, nevrátí `ViewData["RowsAffected"]`, a zobrazení zobrazí prázdné textové pole a tlačítko pro odeslání, jak je znázorněno na předchozím obrázku.</span><span class="sxs-lookup"><span data-stu-id="95a3d-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="95a3d-157">Když **aktualizace** po kliknutí na tlačítko, je volána metoda HttpPost a multiplikátor má hodnota zadaná v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="95a3d-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="95a3d-158">Kód spustí SQL, který aktualizuje kurzy a vrátí počet ovlivněných řádků na zobrazení `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="95a3d-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="95a3d-159">Při zobrazení získá `RowsAffected` hodnotu, zobrazí počet aktualizovaných řádků.</span><span class="sxs-lookup"><span data-stu-id="95a3d-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="95a3d-160">V **Průzkumníku řešení**, klikněte pravým tlačítkem *zobrazení/kurzy* složku a pak klikněte na tlačítko **Přidat > Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="95a3d-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="95a3d-161">V **přidat novou položku** dialogového okna, klikněte na tlačítko **ASP.NET Core** pod **nainstalováno** v levém podokně klikněte na tlačítko **zobrazení Razor**a pojmenujte nové zobrazení  *UpdateCourseCredits.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="95a3d-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="95a3d-162">V *Views/Courses/UpdateCourseCredits.cshtml*, nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="95a3d-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="95a3d-163">Spustit `UpdateCourseCredits` metodu tak, že vyberete **kurzy** kartu a následným přidáním "/ UpdateCourseCredits" na konci adresy URL v adresním řádku prohlížeče (například: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="95a3d-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="95a3d-164">Zadejte číslo v textovém poli:</span><span class="sxs-lookup"><span data-stu-id="95a3d-164">Enter a number in the text box:</span></span>

![Stránka pro aktualizaci kurzu kredity](advanced/_static/update-credits.png)

<span data-ttu-id="95a3d-166">Klikněte na tlačítko **aktualizace**.</span><span class="sxs-lookup"><span data-stu-id="95a3d-166">Click **Update**.</span></span> <span data-ttu-id="95a3d-167">Zobrazí počet ovlivněných řádků:</span><span class="sxs-lookup"><span data-stu-id="95a3d-167">You see the number of rows affected:</span></span>

![Počet ovlivněných řádků kurzu kredity stránku aktualizace](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="95a3d-169">Klikněte na tlačítko **zpět do seznamu** zobrazíte seznam kurzů s revidované množství kreditu, které.</span><span class="sxs-lookup"><span data-stu-id="95a3d-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="95a3d-170">Všimněte si, že produkčního kódu zajistí, že se aktualizace vždy výsledkem platná data.</span><span class="sxs-lookup"><span data-stu-id="95a3d-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="95a3d-171">Zjednodušené kód zobrazený zde vynásobit počet dostatečně kredity za následek čísla větší než 5.</span><span class="sxs-lookup"><span data-stu-id="95a3d-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="95a3d-172">( `Credits` Má vlastnost `[Range(0, 5)]` atribut.) Dotaz update bude fungovat, ale neplatná data může vést k neočekávaným výsledkům v jiných částí systému, které předpokládají, že je číslo kredity ve výši 5 nebo menší.</span><span class="sxs-lookup"><span data-stu-id="95a3d-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="95a3d-173">Další informace o nezpracované dotazy SQL najdete v tématu [nezpracované dotazy SQL](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="95a3d-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="95a3d-174">Zkontrolujte dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="95a3d-174">Examine SQL queries</span></span>

<span data-ttu-id="95a3d-175">Někdy je vhodné se může zobrazit skutečné dotazy SQL, které se odesílají do databáze.</span><span class="sxs-lookup"><span data-stu-id="95a3d-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="95a3d-176">Funkce vestavěné protokolování pro ASP.NET Core je automaticky používá EF Core zápis protokolů, které obsahují SQL pro dotazy a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="95a3d-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="95a3d-177">V této části uvidíte několik příkladů protokolování SQL.</span><span class="sxs-lookup"><span data-stu-id="95a3d-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="95a3d-178">Otevřít *StudentsController.cs* a `Details` metody nastavte zarážku na `if (student == null)` příkazu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="95a3d-179">Spusťte aplikaci v režimu ladění a přejděte na stránku podrobností pro student.</span><span class="sxs-lookup"><span data-stu-id="95a3d-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="95a3d-180">Přejděte **výstup** zobrazující ladění okna výstupu a zobrazit dotaz:</span><span class="sxs-lookup"><span data-stu-id="95a3d-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

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

<span data-ttu-id="95a3d-181">Můžete si všimnout sem něco, co možná vás překvapí: SQL vybere až 2 řádky (`TOP(2)`) z tabulky osob.</span><span class="sxs-lookup"><span data-stu-id="95a3d-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="95a3d-182">`SingleOrDefaultAsync` Metoda nepřekládá na 1 řádek na serveru.</span><span class="sxs-lookup"><span data-stu-id="95a3d-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="95a3d-183">Tady jsou důvody:</span><span class="sxs-lookup"><span data-stu-id="95a3d-183">Here's why:</span></span>

* <span data-ttu-id="95a3d-184">Pokud dotaz by vrátil více řádků, metoda vrátí hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="95a3d-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="95a3d-185">Pokud chcete zjistit, zda dotaz by vrátil více řádků, EF má na registraci, pokud vrací alespoň 2.</span><span class="sxs-lookup"><span data-stu-id="95a3d-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="95a3d-186">Všimněte si, že není nutné použít režim ladění a zastavení na zarážce k získání výstupu protokolování **výstup** okna.</span><span class="sxs-lookup"><span data-stu-id="95a3d-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="95a3d-187">Je jenom pohodlný způsob, jak zastavit protokolování v okamžiku, kdy chcete výstup.</span><span class="sxs-lookup"><span data-stu-id="95a3d-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="95a3d-188">Pokud není to uděláte, pokračuje v protokolování a budete muset přejděte zpět a najděte částí, které vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="95a3d-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="95a3d-189">Vytvořit vrstvu HAL</span><span class="sxs-lookup"><span data-stu-id="95a3d-189">Create an abstraction layer</span></span>

<span data-ttu-id="95a3d-190">Mnoho vývojářů napsat kód pro implementaci úložiště a jednotky pracovních vzorů jako obálka kolem kód, který funguje s Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="95a3d-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="95a3d-191">Tyto vzory jsou určená k vytvoření abstraktní vrstvu mezi vrstva přístupu k datům a vrstvu obchodní logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="95a3d-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="95a3d-192">Implementaci těchto vzorců může pomoci izolovat aplikace před změnami v úložišti dat a mohou usnadnit automatizované testování částí nebo testy řízený vývoj (TDD).</span><span class="sxs-lookup"><span data-stu-id="95a3d-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="95a3d-193">Ale psaním dalšího kódu k implementaci těchto vzorců není vždy nejlepší volbou pro aplikace, které používají EF, z několika důvodů:</span><span class="sxs-lookup"><span data-stu-id="95a3d-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="95a3d-194">Vlastní třídy kontextu EF insulates kódu z konkrétní data úložiště kódu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="95a3d-195">Třídy kontextu EF může fungovat jako třída jednotek práce pro databázi aktualizace, který používáte pomocí EF.</span><span class="sxs-lookup"><span data-stu-id="95a3d-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="95a3d-196">EF obsahuje funkce pro implementaci TDD bez psaní kódu v úložišti.</span><span class="sxs-lookup"><span data-stu-id="95a3d-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="95a3d-197">Informace o tom, jak implementovat úložiště a jednotky pracovních vzorů najdete v tématu [verze Entity Framework 5 v této sérii kurzů](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="95a3d-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="95a3d-198">Entity Framework Core implementuje poskytovatele databáze v paměti, který lze použít pro testování.</span><span class="sxs-lookup"><span data-stu-id="95a3d-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="95a3d-199">Další informace najdete v tématu [Test s InMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="95a3d-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="95a3d-200">Automaticky změnit zjišťování</span><span class="sxs-lookup"><span data-stu-id="95a3d-200">Automatic change detection</span></span>

<span data-ttu-id="95a3d-201">Entity Framework Určuje, jak se entity změnil (a proto aktualizací musí být odeslán do databáze) srovnáním aktuální entity s původními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="95a3d-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="95a3d-202">Původní hodnoty jsou uloženy při entity je dotazovat nebo připojen.</span><span class="sxs-lookup"><span data-stu-id="95a3d-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="95a3d-203">Některé metody, které způsobí automatickou změnu zjišťování jsou následující:</span><span class="sxs-lookup"><span data-stu-id="95a3d-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="95a3d-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="95a3d-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="95a3d-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="95a3d-205">DbContext.Entry</span></span>

* <span data-ttu-id="95a3d-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="95a3d-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="95a3d-207">Pokud sledujete velké množství entit a volání jedné z těchto metod v mnoha případech ve smyčce, můžete dosáhnout výrazné zlepšení výkonu dočasně vypnout automatickou změnu pomocí zjišťování `ChangeTracker.AutoDetectChangesEnabled` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="95a3d-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="95a3d-208">Příklad:</span><span class="sxs-lookup"><span data-stu-id="95a3d-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="95a3d-209">EF Core zdrojového kódu a vývoj plány</span><span class="sxs-lookup"><span data-stu-id="95a3d-209">EF Core source code and development plans</span></span>

<span data-ttu-id="95a3d-210">Zdrojové Entity Framework Core je na [ https://github.com/aspnet/EntityFrameworkCore ](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="95a3d-210">The Entity Framework Core source is at [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span></span> <span data-ttu-id="95a3d-211">EF Core úložiště obsahuje denně automatizovaných buildů, sledování problémů, funkce specifikace, poznámky, návrhu a [plán pro budoucí vývoj](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="95a3d-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span></span> <span data-ttu-id="95a3d-212">Soubor nebo najít chyby a přispívat.</span><span class="sxs-lookup"><span data-stu-id="95a3d-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="95a3d-213">I když se zdrojový kód je otevřený, Entity Framework Core plně podporovat jako produkt společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="95a3d-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="95a3d-214">Tým Microsoft Entity Framework udržuje ovládacího prvku nad tím, které jsou přijaty příspěvky a testy k zajištění kvality každé vydané verze všech změn kódu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="95a3d-215">Zpětná analýza z existující databáze</span><span class="sxs-lookup"><span data-stu-id="95a3d-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="95a3d-216">Chcete-li provést zpětnou analýzu datový model, včetně tříd entit z existující databáze, použijte [vygenerované uživatelské rozhraní dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) příkazu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="95a3d-217">Zobrazit [úvodním kurzu](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="95a3d-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="95a3d-218">Zjednodušení kód pomocí dynamických LINQ</span><span class="sxs-lookup"><span data-stu-id="95a3d-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="95a3d-219">[Třetím kurzu této série](sort-filter-page.md) ukazuje, jak napsat kód LINQ podle pevného kódování názvů sloupců v `switch` příkazu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="95a3d-220">Se dvěma sloupci lze vybírat to funguje správně, ale pokud máte mnoho sloupců kód by mohl získat podrobné.</span><span class="sxs-lookup"><span data-stu-id="95a3d-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="95a3d-221">K vyřešení tohoto problému, můžete použít `EF.Property` metody zadejte název vlastnosti jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="95a3d-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="95a3d-222">Pokud chcete vyzkoušet tento přístup, nahraďte `Index` metodu `StudentsController` následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="95a3d-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="95a3d-223">Potvrzení</span><span class="sxs-lookup"><span data-stu-id="95a3d-223">Acknowledgments</span></span>

<span data-ttu-id="95a3d-224">Petr Dykstra a Rick Anderson (twitter @RickAndMSFT) napsal v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="95a3d-225">Rowan Miller, Diegu Vega a ostatní členové týmu, Entity Framework s asistencí s revizemi kódu a pomohl ladění problémů, které vznikly, když jsme se pro kurzy psaní kódu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="95a3d-226">Jan Parente a Paul Goldman pracovali na aktualizaci kurz pro ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="95a3d-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="95a3d-227">Řešení běžných chyb</span><span class="sxs-lookup"><span data-stu-id="95a3d-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="95a3d-228">ContosoUniversity.dll používá jiný proces</span><span class="sxs-lookup"><span data-stu-id="95a3d-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="95a3d-229">Chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="95a3d-229">Error message:</span></span>

> <span data-ttu-id="95a3d-230">Nelze otevřít "... bin\Debug\netcoreapp1.0\ContosoUniversity.dll" pro zápis--"proces nemůže otevřít soubor"... \bin\Debug\netcoreapp1.0\ContosoUniversity.dll' protože je používán jiným procesem.</span><span class="sxs-lookup"><span data-stu-id="95a3d-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="95a3d-231">Řešení:</span><span class="sxs-lookup"><span data-stu-id="95a3d-231">Solution:</span></span>

<span data-ttu-id="95a3d-232">Zastavte lokalitu ve službě IIS Express.</span><span class="sxs-lookup"><span data-stu-id="95a3d-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="95a3d-233">Přejít na hlavním panelu systému Windows najít službu IIS Express a klikněte pravým tlačítkem na ikonu, vyberte lokalitu vysoké školy Contoso a potom klikněte na tlačítko **zastavení webu**.</span><span class="sxs-lookup"><span data-stu-id="95a3d-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="95a3d-234">Migrace generované uživatelské rozhraní bez kódu v nahoru a dolů metody</span><span class="sxs-lookup"><span data-stu-id="95a3d-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="95a3d-235">Možné příčiny:</span><span class="sxs-lookup"><span data-stu-id="95a3d-235">Possible cause:</span></span>

<span data-ttu-id="95a3d-236">Příkazy rozhraní příkazového řádku EF není automaticky zavřete a uložte soubory s kódem.</span><span class="sxs-lookup"><span data-stu-id="95a3d-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="95a3d-237">Pokud máte neuložené změny při spuštění `migrations add` příkazu EF nenajde provedené změny.</span><span class="sxs-lookup"><span data-stu-id="95a3d-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="95a3d-238">Řešení:</span><span class="sxs-lookup"><span data-stu-id="95a3d-238">Solution:</span></span>

<span data-ttu-id="95a3d-239">Spustit `migrations remove` příkazu, uložte provedené změny kódu a znovu spustit `migrations add` příkazu.</span><span class="sxs-lookup"><span data-stu-id="95a3d-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="95a3d-240">Chyby při spuštění aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="95a3d-240">Errors while running database update</span></span>

<span data-ttu-id="95a3d-241">Je možné získat další chyby při provedení změn schématu v databázi, která obsahuje už existující data.</span><span class="sxs-lookup"><span data-stu-id="95a3d-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="95a3d-242">Pokud se zobrazí chyby při migraci, které nelze vyřešit, můžete změnit název databáze v připojovacím řetězci nebo odstranit databázi.</span><span class="sxs-lookup"><span data-stu-id="95a3d-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="95a3d-243">S novou databázi nejsou žádná data k migraci a příkaz aktualizace databáze je mnohem pravděpodobnější k dokončení bez chyb.</span><span class="sxs-lookup"><span data-stu-id="95a3d-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="95a3d-244">Nejjednodušším způsobem je přejmenovat databázi v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="95a3d-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="95a3d-245">Při příštím spuštění `database update`, vytvoří se nová databáze.</span><span class="sxs-lookup"><span data-stu-id="95a3d-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="95a3d-246">Odstranění databáze v SSOX, klikněte pravým tlačítkem na databázi, klikněte na tlačítko **odstranit**a pak v **odstranit databázi** dialogového okna vyberte **ukončete stávající připojení** a klikněte na tlačítko  **OK**.</span><span class="sxs-lookup"><span data-stu-id="95a3d-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="95a3d-247">Pokud chcete odstranit databázi pomocí rozhraní příkazového řádku, spusťte `database drop` příkazu rozhraní příkazového řádku:</span><span class="sxs-lookup"><span data-stu-id="95a3d-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="95a3d-248">Chyba vyhledáním instanci systému SQL Server</span><span class="sxs-lookup"><span data-stu-id="95a3d-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="95a3d-249">Chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="95a3d-249">Error Message:</span></span>

> <span data-ttu-id="95a3d-250">Při navazování připojení k serveru SQL Server došlo k chybě související se sítí nebo s instancí.</span><span class="sxs-lookup"><span data-stu-id="95a3d-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="95a3d-251">Server nebyl nalezen nebo nebyl přístupný.</span><span class="sxs-lookup"><span data-stu-id="95a3d-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="95a3d-252">Ověřte, zda je název instance správný a, SQL Server je nakonfigurován pro povolit vzdálená připojení.</span><span class="sxs-lookup"><span data-stu-id="95a3d-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="95a3d-253">(poskytovatel: Rozhraní sítě systému SQL, chyba: 26 - Chyba při vyhledávání zadaného serveru či Instance)</span><span class="sxs-lookup"><span data-stu-id="95a3d-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="95a3d-254">Řešení:</span><span class="sxs-lookup"><span data-stu-id="95a3d-254">Solution:</span></span>

<span data-ttu-id="95a3d-255">Zkontrolujte připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="95a3d-255">Check the connection string.</span></span> <span data-ttu-id="95a3d-256">Pokud jste ručně odstranili databázový soubor, změňte název databáze v řetězci konstrukce začít znovu s novou databázi.</span><span class="sxs-lookup"><span data-stu-id="95a3d-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="95a3d-257">Získat kód</span><span class="sxs-lookup"><span data-stu-id="95a3d-257">Get the code</span></span>

[<span data-ttu-id="95a3d-258">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="95a3d-258">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="95a3d-259">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="95a3d-259">Additional resources</span></span>

<span data-ttu-id="95a3d-260">Další informace o EF Core najdete v článku [dokumentace Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="95a3d-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="95a3d-261">Kniha je také k dispozici: [Entity Framework Core v akci](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="95a3d-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="95a3d-262">Informace o tom, jak nasadit webovou aplikaci, najdete v části <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="95a3d-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="95a3d-263">Informace o dalších témat souvisejících s ASP.NET Core MVC, jako je například ověřování a autorizaci, najdete v části <xref:index>.</span><span class="sxs-lookup"><span data-stu-id="95a3d-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="95a3d-264">Další kroky</span><span class="sxs-lookup"><span data-stu-id="95a3d-264">Next steps</span></span>

<span data-ttu-id="95a3d-265">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="95a3d-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="95a3d-266">Prováděné nezpracované dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="95a3d-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="95a3d-267">Volá se dotaz, který vrací entity</span><span class="sxs-lookup"><span data-stu-id="95a3d-267">Called a query to return entities</span></span>
> * <span data-ttu-id="95a3d-268">Volá se dotaz, který jsou vraceny další typy</span><span class="sxs-lookup"><span data-stu-id="95a3d-268">Called a query to return other types</span></span>
> * <span data-ttu-id="95a3d-269">Volá se, k aktualizaci dotazu</span><span class="sxs-lookup"><span data-stu-id="95a3d-269">Called an update query</span></span>
> * <span data-ttu-id="95a3d-270">Přezkoumán dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="95a3d-270">Examined SQL queries</span></span>
> * <span data-ttu-id="95a3d-271">Vytvořit vrstvu HAL</span><span class="sxs-lookup"><span data-stu-id="95a3d-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="95a3d-272">Dozvěděli jste se o změnu automatické zjišťování</span><span class="sxs-lookup"><span data-stu-id="95a3d-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="95a3d-273">Dozvěděli jste se o EF Core zdrojového kódu a vývoj plány</span><span class="sxs-lookup"><span data-stu-id="95a3d-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="95a3d-274">Zjistili jste, jak používat dynamické LINQ pro zjednodušení kódu</span><span class="sxs-lookup"><span data-stu-id="95a3d-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="95a3d-275">Dokončení tohoto postupu Tato série kurzů týkající se použití Entity Framework Core v aplikaci ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="95a3d-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="95a3d-276">Pokud chcete další informace o použití EF 6 pomocí ASP.NET Core, najdete v následujícím článku.</span><span class="sxs-lookup"><span data-stu-id="95a3d-276">If you want to learn about using EF 6 with ASP.NET Core, see the next article.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="95a3d-277">EF 6 s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95a3d-277">EF 6 with ASP.NET Core</span></span>](../entity-framework-6.md)
