---
title: 'Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core'
description: V tomto kurzu se seznámíte s užitečnými tématy, která se týkají vývoje ASP.NET Core webových aplikací, které používají Entity Framework Core.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: 913511948def86cb2065459a3fcf5f16362a92d5
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975036"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f656f-103">Kurz: Další informace o pokročilých scénářích – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="f656f-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f656f-104">V předchozím kurzu jste implementovali dědičnost tabulek na hierarchii.</span><span class="sxs-lookup"><span data-stu-id="f656f-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="f656f-105">V tomto kurzu se seznámíte s několika tématy, která jsou užitečná, když překročíte základy vývoje ASP.NET Core webových aplikací, které používají Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f656f-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="f656f-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="f656f-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f656f-107">Provádění nezpracovaných dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="f656f-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="f656f-108">Volání dotazu pro vrácení entit</span><span class="sxs-lookup"><span data-stu-id="f656f-108">Call a query to return entities</span></span>
> * <span data-ttu-id="f656f-109">Volání dotazu pro vrácení jiných typů</span><span class="sxs-lookup"><span data-stu-id="f656f-109">Call a query to return other types</span></span>
> * <span data-ttu-id="f656f-110">Volání aktualizačního dotazu</span><span class="sxs-lookup"><span data-stu-id="f656f-110">Call an update query</span></span>
> * <span data-ttu-id="f656f-111">Kontrola dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="f656f-111">Examine SQL queries</span></span>
> * <span data-ttu-id="f656f-112">Vytvoření vrstvy abstrakce</span><span class="sxs-lookup"><span data-stu-id="f656f-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="f656f-113">Další informace o automatickém zjišťování změn</span><span class="sxs-lookup"><span data-stu-id="f656f-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="f656f-114">Další informace o EF Core zdrojového kódu a vývojářských plánech</span><span class="sxs-lookup"><span data-stu-id="f656f-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="f656f-115">Naučte se používat dynamickou technologii LINQ ke zjednodušení kódu</span><span class="sxs-lookup"><span data-stu-id="f656f-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f656f-116">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f656f-116">Prerequisites</span></span>

* [<span data-ttu-id="f656f-117">Implementovat dědičnost</span><span class="sxs-lookup"><span data-stu-id="f656f-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="f656f-118">Provádění nezpracovaných dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="f656f-118">Perform raw SQL queries</span></span>

<span data-ttu-id="f656f-119">Jednou z výhod používání Entity Framework je, že se vyhnete tomu, že váš kód je příliš úzce k určité metodě ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="f656f-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="f656f-120">Provede to tím, že vygeneruje dotazy a příkazy SQL za vás, což vám taky zabrání v jejich psaní.</span><span class="sxs-lookup"><span data-stu-id="f656f-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="f656f-121">Existují však výjimečné scénáře, pokud potřebujete spustit konkrétní dotazy SQL, které jste vytvořili ručně.</span><span class="sxs-lookup"><span data-stu-id="f656f-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="f656f-122">V těchto scénářích obsahuje rozhraní API Entity Framework Code First metody, které vám umožní předat příkazy SQL přímo do databáze.</span><span class="sxs-lookup"><span data-stu-id="f656f-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="f656f-123">V EF Core 1,0 máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="f656f-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="f656f-124">`DbSet.FromSql` Použijte metodu pro dotazy, které vracejí typy entit.</span><span class="sxs-lookup"><span data-stu-id="f656f-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="f656f-125">Vrácené objekty musí být typu očekávaného `DbSet` objektem a automaticky sledovány pomocí kontextu databáze, pokud nevypnete [sledování](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="f656f-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="f656f-126">Použijte příkaz `Database.ExecuteSqlCommand` pro příkazy, které nejsou dotazem.</span><span class="sxs-lookup"><span data-stu-id="f656f-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="f656f-127">Pokud potřebujete spustit dotaz, který vrací typy, které nejsou entitami, můžete použít ADO.NET s databázovým připojením poskytovaným EF.</span><span class="sxs-lookup"><span data-stu-id="f656f-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="f656f-128">Vrácená data nejsou sledována kontextem databáze, a to i v případě, že použijete tuto metodu k načtení typů entit.</span><span class="sxs-lookup"><span data-stu-id="f656f-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="f656f-129">Jak je vždy true při provádění příkazů SQL ve webové aplikaci, je nutné podniknout preventivní opatření k ochraně vašeho webu před útoky prostřednictvím injektáže SQL.</span><span class="sxs-lookup"><span data-stu-id="f656f-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="f656f-130">Jedním ze způsobů, jak to provést, je použít parametrizované dotazy, aby se zajistilo, že řetězce odeslané webovou stránkou nejde interpretovat jako příkazy SQL.</span><span class="sxs-lookup"><span data-stu-id="f656f-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="f656f-131">V tomto kurzu použijete parametrizované dotazy při integraci vstupu uživatele do dotazu.</span><span class="sxs-lookup"><span data-stu-id="f656f-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="f656f-132">Volání dotazu pro vrácení entit</span><span class="sxs-lookup"><span data-stu-id="f656f-132">Call a query to return entities</span></span>

<span data-ttu-id="f656f-133">Třída poskytuje metodu, kterou lze použít ke spuštění dotazu, který vrací entitu typu `TEntity`. `DbSet<TEntity>`</span><span class="sxs-lookup"><span data-stu-id="f656f-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="f656f-134">Chcete-li zjistit, jak to funguje, změňte kód v `Details` metodě řadiče oddělení.</span><span class="sxs-lookup"><span data-stu-id="f656f-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="f656f-135">V *DepartmentsController.cs*v `Details` metodě nahraďte kód, který získá oddělení, pomocí `FromSql` volání metody, jak je znázorněno v následujícím zvýrazněném kódu:</span><span class="sxs-lookup"><span data-stu-id="f656f-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="f656f-136">Chcete-li ověřit, zda nový kód funguje správně, vyberte kartu **oddělení** a potom **Podrobnosti** pro jedno z oddělení.</span><span class="sxs-lookup"><span data-stu-id="f656f-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Podrobnosti o oddělení](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="f656f-138">Volání dotazu pro vrácení jiných typů</span><span class="sxs-lookup"><span data-stu-id="f656f-138">Call a query to return other types</span></span>

<span data-ttu-id="f656f-139">Dříve jste vytvořili tabulku statistik studentů pro stránku o produktu, která ukázala počet studentů pro každé datum registrace.</span><span class="sxs-lookup"><span data-stu-id="f656f-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="f656f-140">Dostali jste data ze sady entit Students (`_context.Students`) a pomocí LINQ můžete promítnout výsledky do `EnrollmentDateGroup` seznamu objektů modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f656f-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="f656f-141">Předpokládejme, že chcete napsat samotný SQL místo použití LINQ.</span><span class="sxs-lookup"><span data-stu-id="f656f-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="f656f-142">K tomu je nutné spustit dotaz SQL, který vrací jinou hodnotu než objekty entity.</span><span class="sxs-lookup"><span data-stu-id="f656f-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="f656f-143">V EF Core 1,0 je jedním ze způsobů, jak to udělat, je zápis ADO.NET kódu a získání připojení k databázi z EF.</span><span class="sxs-lookup"><span data-stu-id="f656f-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="f656f-144">V *HomeController.cs*nahraďte `About` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f656f-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="f656f-145">Přidat sadu pomocí příkazu:</span><span class="sxs-lookup"><span data-stu-id="f656f-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="f656f-146">Spusťte aplikaci a pokračujte na stránku o produktu.</span><span class="sxs-lookup"><span data-stu-id="f656f-146">Run the app and go to the About page.</span></span> <span data-ttu-id="f656f-147">Zobrazuje stejná data jako předtím.</span><span class="sxs-lookup"><span data-stu-id="f656f-147">It displays the same data it did before.</span></span>

![O stránce](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="f656f-149">Volání aktualizačního dotazu</span><span class="sxs-lookup"><span data-stu-id="f656f-149">Call an update query</span></span>

<span data-ttu-id="f656f-150">Předpokládejme, že správci služby contoso University chtějí v databázi provádět globální změny, jako je třeba Změna počtu kreditů pro každý kurz.</span><span class="sxs-lookup"><span data-stu-id="f656f-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="f656f-151">Pokud má univerzita velký počet kurzů, je třeba je neefektivně načíst jako entity a jednotlivě je měnit.</span><span class="sxs-lookup"><span data-stu-id="f656f-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="f656f-152">V této části implementujete webovou stránku, která uživateli umožní zadat faktor, podle kterého se má změnit počet kreditů pro všechny kurzy, a provedete změnu provedením příkazu SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="f656f-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="f656f-153">Webová stránka bude vypadat jako na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="f656f-153">The web page will look like the following illustration:</span></span>

![Stránka aktualizovat kredity kurzu](advanced/_static/update-credits.png)

<span data-ttu-id="f656f-155">V *CoursesController.cs*přidejte metody UpdateCourseCredits pro HttpGet a HTTPPOST:</span><span class="sxs-lookup"><span data-stu-id="f656f-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="f656f-156">Když kontroler zpracuje požadavek HttpGet, nic se nevrátí `ViewData["RowsAffected"]`a v zobrazení se zobrazí prázdné textové pole a tlačítko Odeslat, jak je znázorněno na předchozím obrázku.</span><span class="sxs-lookup"><span data-stu-id="f656f-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="f656f-157">Po kliknutí na tlačítko **aktualizovat** se zavolá metoda HTTPPOST a násobitel má hodnotu zadanou v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="f656f-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="f656f-158">Kód potom spustí SQL, který aktualizuje kurzy a vrátí počet ovlivněných řádků do zobrazení v `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="f656f-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="f656f-159">Když zobrazení získá `RowsAffected` hodnotu, zobrazí se počet aktualizovaných řádků.</span><span class="sxs-lookup"><span data-stu-id="f656f-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="f656f-160">V **Průzkumník řešení**klikněte pravým tlačítkem na složku *views/kurzy* a pak klikněte na **Přidat > Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="f656f-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="f656f-161">V dialogovém okně **Přidat novou položku** klikněte **ASP.NET Core** v části **nainstalováno** v levém podokně klikněte na **zobrazení Razor**a pojmenujte nové zobrazení *UpdateCourseCredits. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f656f-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="f656f-162">V *zobrazeních/kurzů/UpdateCourseCredits. cshtml*nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f656f-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="f656f-163">Spusťte metodu tak, že vyberete kartu **kurzy** a pak na konec adresy URL v adresním řádku prohlížeče přidáte "/UpdateCourseCredits" (například: `http://localhost:5813/Courses/UpdateCourseCredits`). `UpdateCourseCredits`</span><span class="sxs-lookup"><span data-stu-id="f656f-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="f656f-164">Do textového pole zadejte číslo:</span><span class="sxs-lookup"><span data-stu-id="f656f-164">Enter a number in the text box:</span></span>

![Stránka aktualizovat kredity kurzu](advanced/_static/update-credits.png)

<span data-ttu-id="f656f-166">Klikněte na tlačítko **aktualizace**.</span><span class="sxs-lookup"><span data-stu-id="f656f-166">Click **Update**.</span></span> <span data-ttu-id="f656f-167">Zobrazí se počet ovlivněných řádků:</span><span class="sxs-lookup"><span data-stu-id="f656f-167">You see the number of rows affected:</span></span>

![Aktualizace ovlivněných řádků na stránce kurzů](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="f656f-169">Kliknutím na **zpět na seznam** zobrazíte seznam kurzů s revidovaným počtem kreditů.</span><span class="sxs-lookup"><span data-stu-id="f656f-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="f656f-170">Všimněte si, že produkční kód zajistí, že aktualizace vždy mají za následek platná data.</span><span class="sxs-lookup"><span data-stu-id="f656f-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="f656f-171">Zde zobrazený zjednodušený kód může vynásobit počet kreditů, které jsou dostatečné k tomu, aby byly čísla větší než 5.</span><span class="sxs-lookup"><span data-stu-id="f656f-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="f656f-172">`Credits` ( Vlastnost`[Range(0, 5)]` má atribut.) Aktualizační dotaz by mohl fungovat, ale neplatná data by mohla způsobit neočekávané výsledky v jiných částech systému, které předpokládají, že počet kreditů je 5 nebo méně.</span><span class="sxs-lookup"><span data-stu-id="f656f-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="f656f-173">Další informace o nezpracovaných dotazech SQL naleznete v tématu [raw SQL dotazy](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="f656f-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="f656f-174">Kontrola dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="f656f-174">Examine SQL queries</span></span>

<span data-ttu-id="f656f-175">Někdy je užitečné, abyste si mohli prohlédnout skutečné dotazy SQL, které se odesílají do databáze.</span><span class="sxs-lookup"><span data-stu-id="f656f-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="f656f-176">Integrovaná funkce protokolování pro ASP.NET Core automaticky používá EF Core k zápisu protokolů, které obsahují SQL pro dotazy a aktualizace.</span><span class="sxs-lookup"><span data-stu-id="f656f-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="f656f-177">V této části se zobrazí některé příklady protokolování SQL.</span><span class="sxs-lookup"><span data-stu-id="f656f-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="f656f-178">Otevřete *StudentsController.cs* a v `Details` metodě nastavte zarážku na `if (student == null)` příkazu.</span><span class="sxs-lookup"><span data-stu-id="f656f-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="f656f-179">Spusťte aplikaci v režimu ladění a pokračujte na stránku podrobností pro studenta.</span><span class="sxs-lookup"><span data-stu-id="f656f-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="f656f-180">Přejděte do okna **výstup** zobrazující výstup ladění a zobrazí se dotaz:</span><span class="sxs-lookup"><span data-stu-id="f656f-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

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

<span data-ttu-id="f656f-181">Všimnete si, že se vám může stát, že váš příkaz SQL vybere až 2 řádky (`TOP(2)`) z tabulky Person.</span><span class="sxs-lookup"><span data-stu-id="f656f-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="f656f-182">`SingleOrDefaultAsync` Metoda není přeložena na 1 řádek na serveru.</span><span class="sxs-lookup"><span data-stu-id="f656f-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="f656f-183">Důvody:</span><span class="sxs-lookup"><span data-stu-id="f656f-183">Here's why:</span></span>

* <span data-ttu-id="f656f-184">Pokud by dotaz vrátil více řádků, vrátí metoda hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f656f-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="f656f-185">Chcete-li zjistit, zda dotaz by vrátil více řádků, EF musí ověřit, zda se vrátí alespoň 2.</span><span class="sxs-lookup"><span data-stu-id="f656f-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="f656f-186">Všimněte si, že nemusíte používat režim ladění a zastavte zarážku, abyste získali výstup protokolování v okně **výstup** .</span><span class="sxs-lookup"><span data-stu-id="f656f-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="f656f-187">Je to jenom pohodlný způsob, jak zastavit protokolování v místě, kde chcete podívat na výstup.</span><span class="sxs-lookup"><span data-stu-id="f656f-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="f656f-188">Pokud to neuděláte, budete pokračovat v protokolování a budete muset přejít zpátky a vyhledat části, které vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="f656f-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="f656f-189">Vytvoření vrstvy abstrakce</span><span class="sxs-lookup"><span data-stu-id="f656f-189">Create an abstraction layer</span></span>

<span data-ttu-id="f656f-190">Mnoho vývojářů napíše kód, který implementuje úložiště a pracovní postupy jako obálku kolem kódu, který pracuje s Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f656f-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="f656f-191">Tyto vzory mají za cíl vytvořit vrstvu abstrakce mezi vrstvou pro přístup k datům a vrstvou obchodní logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f656f-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="f656f-192">Implementace těchto vzorů vám může přispět k izolaci aplikace před změnami v úložišti dat a může usnadnit automatizované testování částí nebo vývoj řízený testováním (TDD).</span><span class="sxs-lookup"><span data-stu-id="f656f-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="f656f-193">Nicméně psaní dalšího kódu pro implementaci těchto vzorů není vždy nejlepší volbou pro aplikace, které používají EF, z několika důvodů:</span><span class="sxs-lookup"><span data-stu-id="f656f-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="f656f-194">Třída kontextu EF sama neizolací váš kód z kódu specifického pro úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="f656f-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="f656f-195">Třída kontextu EF může fungovat jako jednotka pro práci s aktualizacemi databáze, které používáte pomocí EF.</span><span class="sxs-lookup"><span data-stu-id="f656f-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="f656f-196">EF obsahuje funkce pro implementaci služby TDD bez psaní kódu úložiště.</span><span class="sxs-lookup"><span data-stu-id="f656f-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="f656f-197">Informace o implementaci vzorového úložiště a pracovní jednotky najdete v části [Entity Framework 5 této série kurzů](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="f656f-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="f656f-198">Entity Framework Core implementuje poskytovatele databáze v paměti, který lze použít k testování.</span><span class="sxs-lookup"><span data-stu-id="f656f-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="f656f-199">Další informace najdete v tématu [test s](/ef/core/miscellaneous/testing/in-memory)nepamětí.</span><span class="sxs-lookup"><span data-stu-id="f656f-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="f656f-200">Automatické zjišťování změn</span><span class="sxs-lookup"><span data-stu-id="f656f-200">Automatic change detection</span></span>

<span data-ttu-id="f656f-201">Entity Framework určuje, jak se entita změnila (takže se aktualizace musí odeslat do databáze) porovnáním aktuálních hodnot entity s původními hodnotami.</span><span class="sxs-lookup"><span data-stu-id="f656f-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="f656f-202">Původní hodnoty se uloží, když je entita dotazována nebo připojena.</span><span class="sxs-lookup"><span data-stu-id="f656f-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="f656f-203">Některé z metod, které způsobují automatickou detekci změn, jsou následující:</span><span class="sxs-lookup"><span data-stu-id="f656f-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="f656f-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f656f-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="f656f-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="f656f-205">DbContext.Entry</span></span>

* <span data-ttu-id="f656f-206">ChangeTracker. Entries</span><span class="sxs-lookup"><span data-stu-id="f656f-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="f656f-207">Pokud sledujete velký počet entit a v rámci smyčky několikrát voláte jednu z těchto metod, můžete dosáhnout výrazného zlepšení výkonu tím, že se `ChangeTracker.AutoDetectChangesEnabled` při automatickém vypnutí automatického zjišťování změn použije vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f656f-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="f656f-208">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f656f-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="f656f-209">EF Core zdrojový kód a vývojové plány</span><span class="sxs-lookup"><span data-stu-id="f656f-209">EF Core source code and development plans</span></span>

<span data-ttu-id="f656f-210">Zdroj Entity Framework Core je v [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="f656f-210">The Entity Framework Core source is at [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span></span> <span data-ttu-id="f656f-211">EF Core úložiště obsahuje noční buildy, sledování problémů, specifikace funkcí, návrhy poznámek na schůzce a [plán pro budoucí vývoj](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="f656f-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span></span> <span data-ttu-id="f656f-212">Můžete soubor nebo najít chyby a přispívat.</span><span class="sxs-lookup"><span data-stu-id="f656f-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="f656f-213">I když je zdrojový kód otevřený, Entity Framework Core je plně podporovaný jako produkt společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f656f-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="f656f-214">Tým Microsoft Entity Framework udržuje kontrolu nad tím, které příspěvky jsou přijaty, a testuje všechny změny kódu, aby se zajistila kvalita jednotlivých verzí.</span><span class="sxs-lookup"><span data-stu-id="f656f-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="f656f-215">Zpětná analýza z existující databáze</span><span class="sxs-lookup"><span data-stu-id="f656f-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="f656f-216">Chcete-li provést zpětnou analýzu datového modelu, včetně tříd entit z existující databáze, použijte příkaz pro [generování uživatelského rozhraní (DbContext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) ).</span><span class="sxs-lookup"><span data-stu-id="f656f-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="f656f-217">Přečtěte si [Úvodní kurz](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="f656f-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="f656f-218">Zjednodušení kódu pomocí dynamického jazyka LINQ</span><span class="sxs-lookup"><span data-stu-id="f656f-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="f656f-219">[Třetí kurz v této sérii](sort-filter-page.md) ukazuje, jak napsat kód LINQ pomocí pevného kódování názvů sloupců v `switch` příkazu.</span><span class="sxs-lookup"><span data-stu-id="f656f-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="f656f-220">Se dvěma sloupci, ze kterých si můžete vybrat, to funguje dobře, ale pokud máte mnoho sloupců, může kód získat podrobné.</span><span class="sxs-lookup"><span data-stu-id="f656f-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="f656f-221">Chcete-li tento problém vyřešit, můžete použít `EF.Property` metodu k určení názvu vlastnosti jako řetězce.</span><span class="sxs-lookup"><span data-stu-id="f656f-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="f656f-222">Chcete-li vyzkoušet tento přístup, nahraďte `Index` metodu `StudentsController` v kódu následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="f656f-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="f656f-223">Potvrzení</span><span class="sxs-lookup"><span data-stu-id="f656f-223">Acknowledgments</span></span>

<span data-ttu-id="f656f-224">V tomto kurzu jste napsali Dykstra @RickAndMSFTa Rick Anderson (Twitter).</span><span class="sxs-lookup"><span data-stu-id="f656f-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="f656f-225">Rowan Miller, Diegu Vega a další členové Entity Framework týmu s asistencí revize kódu a pomohli ladit problémy, které vznikly při psaní kódu pro kurzy.</span><span class="sxs-lookup"><span data-stu-id="f656f-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="f656f-226">Jan rodiče a Paul Goldman pracovali na aktualizaci kurzu pro ASP.NET Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="f656f-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="f656f-227">Řešení běžných chyb</span><span class="sxs-lookup"><span data-stu-id="f656f-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="f656f-228">ContosoUniversity. dll používá jiný proces.</span><span class="sxs-lookup"><span data-stu-id="f656f-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="f656f-229">Chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="f656f-229">Error message:</span></span>

> <span data-ttu-id="f656f-230">Nejde otevřít... bin\Debug\netcoreapp1.0\ContosoUniversity.dll ' pro zápis-' proces nemůže získat přístup k souboru '. ..\bin\Debug\netcoreapp1.0\ContosoUniversity.dll ', protože je používán jiným procesem.</span><span class="sxs-lookup"><span data-stu-id="f656f-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="f656f-231">Řešení</span><span class="sxs-lookup"><span data-stu-id="f656f-231">Solution:</span></span>

<span data-ttu-id="f656f-232">Zastavte lokalitu v IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f656f-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="f656f-233">Přejděte na hlavní panel systému Windows, vyhledejte IIS Express a klikněte pravým tlačítkem na jeho ikonu, vyberte web společnosti Contoso University a pak klikněte na **Zastavit Web**.</span><span class="sxs-lookup"><span data-stu-id="f656f-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="f656f-234">Replikace – generování uživatelského rozhraní bez kódu v rámci metod up a Down</span><span class="sxs-lookup"><span data-stu-id="f656f-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="f656f-235">Možná příčina:</span><span class="sxs-lookup"><span data-stu-id="f656f-235">Possible cause:</span></span>

<span data-ttu-id="f656f-236">Příkazy EF CLI automaticky nezavřou a neukládají soubory kódu.</span><span class="sxs-lookup"><span data-stu-id="f656f-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="f656f-237">Pokud máte neuložené změny při spuštění `migrations add` příkazu, EF nezjistí vaše změny.</span><span class="sxs-lookup"><span data-stu-id="f656f-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="f656f-238">Řešení</span><span class="sxs-lookup"><span data-stu-id="f656f-238">Solution:</span></span>

<span data-ttu-id="f656f-239">Spusťte příkaz, uložte změny kódu a `migrations add` spusťte příkaz znovu. `migrations remove`</span><span class="sxs-lookup"><span data-stu-id="f656f-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="f656f-240">Chyby při spuštění aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="f656f-240">Errors while running database update</span></span>

<span data-ttu-id="f656f-241">Při provádění změn schématu v databázi, která obsahuje existující data, je možné získat další chyby.</span><span class="sxs-lookup"><span data-stu-id="f656f-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="f656f-242">Pokud se zobrazí chyby migrace, které nemůžete vyřešit, můžete buď změnit název databáze v připojovacím řetězci, nebo databázi odstranit.</span><span class="sxs-lookup"><span data-stu-id="f656f-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="f656f-243">V případě nové databáze není k dispozici žádná data k migraci a příkaz Update-Database je mnohem pravděpodobnější, že se nedokončí bez chyb.</span><span class="sxs-lookup"><span data-stu-id="f656f-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="f656f-244">Nejjednodušším přístupem je přejmenovat databázi v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="f656f-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="f656f-245">Při příštím spuštění `database update`se vytvoří nová databáze.</span><span class="sxs-lookup"><span data-stu-id="f656f-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="f656f-246">Databázi v SSOX odstraníte tak, že kliknete pravým tlačítkem na databázi, kliknete na **Odstranit**a pak v dialogovém okně **odstranit databázi** vyberete **Zavřít existující připojení** a kliknete na **OK**.</span><span class="sxs-lookup"><span data-stu-id="f656f-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="f656f-247">Chcete-li odstranit databázi pomocí rozhraní příkazového řádku, `database drop` spusťte příkaz CLI:</span><span class="sxs-lookup"><span data-stu-id="f656f-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="f656f-248">Při hledání instance SQL Server došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="f656f-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="f656f-249">Chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="f656f-249">Error Message:</span></span>

> <span data-ttu-id="f656f-250">Při navazování připojení k SQL Server došlo k chybě související se sítí nebo instanci.</span><span class="sxs-lookup"><span data-stu-id="f656f-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="f656f-251">Server nebyl nalezen nebo k němu nelze získat přístup.</span><span class="sxs-lookup"><span data-stu-id="f656f-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="f656f-252">Ověřte, zda je název instance správný a zda je SQL Server nakonfigurovaná tak, aby povolovala vzdálená připojení.</span><span class="sxs-lookup"><span data-stu-id="f656f-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="f656f-253">zprostředkovatele Síťová rozhraní SQL, chyba: 26 – Chyba při hledání zadaného serveru/instance)</span><span class="sxs-lookup"><span data-stu-id="f656f-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="f656f-254">Řešení</span><span class="sxs-lookup"><span data-stu-id="f656f-254">Solution:</span></span>

<span data-ttu-id="f656f-255">Ověřte připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="f656f-255">Check the connection string.</span></span> <span data-ttu-id="f656f-256">Pokud jste soubor databáze odstranili ručně, změňte název databáze v řetězci konstrukce, aby bylo možné začít znovu s novou databází.</span><span class="sxs-lookup"><span data-stu-id="f656f-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="f656f-257">Získat kód</span><span class="sxs-lookup"><span data-stu-id="f656f-257">Get the code</span></span>

[<span data-ttu-id="f656f-258">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="f656f-258">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="f656f-259">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f656f-259">Additional resources</span></span>

<span data-ttu-id="f656f-260">Další informace o EF Core najdete v dokumentaci k [Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="f656f-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="f656f-261">K dispozici je také kniha: [Entity Framework Core v akci](https://www.manning.com/books/entity-framework-core-in-action).</span><span class="sxs-lookup"><span data-stu-id="f656f-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="f656f-262">Informace o tom, jak nasadit webovou aplikaci, najdete v <xref:host-and-deploy/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="f656f-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="f656f-263">Informace o dalších tématech souvisejících s ASP.NET Core MVC, jako je ověřování a autorizace, najdete v <xref:index>tématu.</span><span class="sxs-lookup"><span data-stu-id="f656f-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f656f-264">Další postup</span><span class="sxs-lookup"><span data-stu-id="f656f-264">Next steps</span></span>

<span data-ttu-id="f656f-265">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="f656f-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f656f-266">Provedené nezpracované dotazy SQL</span><span class="sxs-lookup"><span data-stu-id="f656f-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="f656f-267">Volal se dotaz na vrácení entit.</span><span class="sxs-lookup"><span data-stu-id="f656f-267">Called a query to return entities</span></span>
> * <span data-ttu-id="f656f-268">Volal se dotaz, který vrátí jiné typy.</span><span class="sxs-lookup"><span data-stu-id="f656f-268">Called a query to return other types</span></span>
> * <span data-ttu-id="f656f-269">Volal se dotaz na aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="f656f-269">Called an update query</span></span>
> * <span data-ttu-id="f656f-270">Prověření dotazů SQL</span><span class="sxs-lookup"><span data-stu-id="f656f-270">Examined SQL queries</span></span>
> * <span data-ttu-id="f656f-271">Vytvořila se vrstva abstrakce.</span><span class="sxs-lookup"><span data-stu-id="f656f-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="f656f-272">Seznámili jste se o automatické detekci změn</span><span class="sxs-lookup"><span data-stu-id="f656f-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="f656f-273">Dozvěděli jste se o EF Core zdrojového kódu a vývojářských plánech.</span><span class="sxs-lookup"><span data-stu-id="f656f-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="f656f-274">Zjistili jsme, jak pomocí dynamického jazyka LINQ zjednodušit kód</span><span class="sxs-lookup"><span data-stu-id="f656f-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="f656f-275">Tím se dokončí Tato série kurzů na používání Entity Framework Core v aplikaci ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f656f-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="f656f-276">Tato série se pracovala s novou databází. alternativou je provést zpětnou analýzu modelu z existující databáze.</span><span class="sxs-lookup"><span data-stu-id="f656f-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f656f-277">Kurz: EF Core s MVC, existující databází</span><span class="sxs-lookup"><span data-stu-id="f656f-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
