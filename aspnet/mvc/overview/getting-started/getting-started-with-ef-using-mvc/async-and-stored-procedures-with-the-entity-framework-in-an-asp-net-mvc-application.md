---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Kurz: Použití async a uložené procedury s EF v aplikaci ASP.NET MVC'
description: V tomto kurzu můžete zjistit, jak implementovat asynchronní programovací model a zjistěte, jak pomocí uložených procedur.
author: tdykstra
ms.author: riande
ms.date: 01/18/2019
ms.topic: tutorial
ms.assetid: 27d110fc-d1b7-4628-a763-26f1e6087549
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9041167af076d80ebf294e054ffe51293d11e888
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889870"
---
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a><span data-ttu-id="c7c99-103">Kurz: Použití async a uložené procedury s EF v aplikaci ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="c7c99-103">Tutorial: Use async and stored procedures with EF in an ASP.NET MVC App</span></span>

<span data-ttu-id="c7c99-104">V předchozích kurzech jste zjistili, jak číst a aktualizovat data pomocí synchronní programovací model.</span><span class="sxs-lookup"><span data-stu-id="c7c99-104">In earlier tutorials you learned how to read and update data using the synchronous programming model.</span></span> <span data-ttu-id="c7c99-105">V tomto kurzu můžete zjistit, jak implementovat asynchronní programovací model.</span><span class="sxs-lookup"><span data-stu-id="c7c99-105">In this tutorial you see how to implement the asynchronous programming model.</span></span> <span data-ttu-id="c7c99-106">Asynchronní kód může pomoct lépe provést, protože umožňuje lepší využití serverových prostředků aplikace.</span><span class="sxs-lookup"><span data-stu-id="c7c99-106">Asynchronous code can help an application perform better because it makes better use of server resources.</span></span>

<span data-ttu-id="c7c99-107">V tomto kurzu můžete také zjistit, jak pomocí uložené procedury pro vložení, aktualizace nebo odstranění operace s entitou.</span><span class="sxs-lookup"><span data-stu-id="c7c99-107">In this tutorial you also see how to use stored procedures for insert, update, and delete operations on an entity.</span></span>

<span data-ttu-id="c7c99-108">Nakonec znovu nasadit aplikaci do Azure, spolu s všechny změny databáze, které jsme implementovali od první chvíle, kdy jste nasadili.</span><span class="sxs-lookup"><span data-stu-id="c7c99-108">Finally, you redeploy the application to Azure, along with all of the database changes that you've implemented since the first time you deployed.</span></span>

<span data-ttu-id="c7c99-109">Následující ilustrace znázorňují některé stránky, které budete pracovat.</span><span class="sxs-lookup"><span data-stu-id="c7c99-109">The following illustrations show some of the pages that you'll work with.</span></span>

![Oddělení stránky](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Vytvoření oddělení](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="c7c99-112">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="c7c99-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c7c99-113">Další informace o asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="c7c99-113">Learn about asynchronous code</span></span>
> * <span data-ttu-id="c7c99-114">Vytvoření kontroleru oddělení</span><span class="sxs-lookup"><span data-stu-id="c7c99-114">Create a Department controller</span></span>
> * <span data-ttu-id="c7c99-115">Použití uložených procedur</span><span class="sxs-lookup"><span data-stu-id="c7c99-115">Use stored procedures</span></span>
> * <span data-ttu-id="c7c99-116">Nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="c7c99-116">Deploy to Azure</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c7c99-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="c7c99-117">Prerequisites</span></span>

* [<span data-ttu-id="c7c99-118">Aktualizace souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="c7c99-118">Updating Related Data</span></span>](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a><span data-ttu-id="c7c99-119">Proč používat asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="c7c99-119">Why use asynchronous code</span></span>

<span data-ttu-id="c7c99-120">Webový server má omezený počet vláken, které jsou k dispozici, a v situacích, vysokého zatížení všech dostupných vláken může být používán.</span><span class="sxs-lookup"><span data-stu-id="c7c99-120">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="c7c99-121">Pokud k tomu dojde, server nemůže zpracovat nové žádosti, dokud se uvolnit vlákna.</span><span class="sxs-lookup"><span data-stu-id="c7c99-121">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="c7c99-122">Přestože se nejedná skutečně každé dílo vzhledem k tomu, že čekání na vstupně-výstupních operací na dokončení, může kódem synchronní svázané několika vlákny.</span><span class="sxs-lookup"><span data-stu-id="c7c99-122">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="c7c99-123">Asynchronní kód když proces čeká na vstupně-výstupních operací na dokončení, je jeho vlákno uvolněn pro server určený pro zpracováním jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="c7c99-123">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="c7c99-124">V důsledku toho asynchronního kódu umožňuje serveru prostředků efektivněji používat, a abyste zvládli větší provoz bez zpoždění je povoleno na serveru.</span><span class="sxs-lookup"><span data-stu-id="c7c99-124">As a result, asynchronous code enables server resources to be use more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="c7c99-125">V dřívějších verzích rozhraní .NET byl složitý, psaní a testování asynchronní kód chyby náchylné k chybám a těžko ladění.</span><span class="sxs-lookup"><span data-stu-id="c7c99-125">In earlier versions of .NET, writing and testing asynchronous code was complex, error prone, and hard to debug.</span></span> <span data-ttu-id="c7c99-126">V rozhraní .NET 4.5 je mnohem jednodušší, pokud nemáte důvod, proč nebylo by měly obecně psaní asynchronního kódu psaní, testování a ladění asynchronního kódu.</span><span class="sxs-lookup"><span data-stu-id="c7c99-126">In .NET 4.5, writing, testing, and debugging asynchronous code is so much easier that you should generally write asynchronous code unless you have a reason not to.</span></span> <span data-ttu-id="c7c99-127">Asynchronní kód zavést malé množství režie, ale v situacích s nízkým provozem výkonu přístupů je zanedbatelný, při vysoké návštěvnosti situacích, je možné zlepšení výkonu podstatné.</span><span class="sxs-lookup"><span data-stu-id="c7c99-127">Asynchronous code does introduce a small amount of overhead, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="c7c99-128">Další informace o asynchronním programování naleznete v tématu [podpory asynchronních operací pomocí rozhraní .NET 4.5 k zabránění blokování volání](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).</span><span class="sxs-lookup"><span data-stu-id="c7c99-128">For more information about asynchronous programming, see [Use .NET 4.5's async support to avoid blocking calls](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).</span></span>

## <a name="create-department-controller"></a><span data-ttu-id="c7c99-129">Vytvoření kontroleru oddělení</span><span class="sxs-lookup"><span data-stu-id="c7c99-129">Create Department controller</span></span>

<span data-ttu-id="c7c99-130">Vytvoření kontroleru oddělení, stejně jako jste to udělali starších řadičů, tentokrát vyberte **použít asynchronní akce kontroleru** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="c7c99-130">Create a Department controller the same way you did the earlier controllers, except this time select the **Use async controller actions** check box.</span></span>

<span data-ttu-id="c7c99-131">Následujícím hlavním funkcím zobrazit, co byl přidán do synchronní kód `Index` provést asynchronní metody:</span><span class="sxs-lookup"><span data-stu-id="c7c99-131">The following highlights show what was added to the synchronous code for the `Index` method to make it asynchronous:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

<span data-ttu-id="c7c99-132">Čtyři změny byly použity k povolení databázového dotazu Entity Framework spustit asynchronně:</span><span class="sxs-lookup"><span data-stu-id="c7c99-132">Four changes were applied to enable the Entity Framework database query to execute asynchronously:</span></span>

- <span data-ttu-id="c7c99-133">Metoda je označena třídou `async` – klíčové slovo, které instruuje kompilátor generovat zpětná volání pro části tělo metody a automaticky vytvářet `Task<ActionResult>` vráceného objektu.</span><span class="sxs-lookup"><span data-stu-id="c7c99-133">The method is marked with the `async` keyword, which tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<ActionResult>` object that is returned.</span></span>
- <span data-ttu-id="c7c99-134">Návratový typ se změnil z `ActionResult` k `Task<ActionResult>`.</span><span class="sxs-lookup"><span data-stu-id="c7c99-134">The return type was changed from `ActionResult` to `Task<ActionResult>`.</span></span> <span data-ttu-id="c7c99-135">`Task<T>` Typ představuje probíhající práci s výsledkem typu `T`.</span><span class="sxs-lookup"><span data-stu-id="c7c99-135">The `Task<T>` type represents ongoing work with a result of type `T`.</span></span>
- <span data-ttu-id="c7c99-136">`await` – Klíčové slovo byla použita k volání webové služby.</span><span class="sxs-lookup"><span data-stu-id="c7c99-136">The `await` keyword was applied to the web service call.</span></span> <span data-ttu-id="c7c99-137">Když kompilátor narazí toto klíčové slovo, na pozadí rozdělí metodu na dva oddíly.</span><span class="sxs-lookup"><span data-stu-id="c7c99-137">When the compiler sees this keyword, behind the scenes it splits the method into two parts.</span></span> <span data-ttu-id="c7c99-138">První část končí operace, která se spustí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="c7c99-138">The first part ends with the operation that is started asynchronously.</span></span> <span data-ttu-id="c7c99-139">Druhá část je nepoužili metodu zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="c7c99-139">The second part is put into a callback method that is called when the operation completes.</span></span>
- <span data-ttu-id="c7c99-140">Asynchronní verze `ToList` byla volána metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="c7c99-140">The asynchronous version of the `ToList` extension method was called.</span></span>

<span data-ttu-id="c7c99-141">Proč je `departments.ToList` příkaz upravit, ale ne `departments = db.Departments` příkaz?</span><span class="sxs-lookup"><span data-stu-id="c7c99-141">Why is the `departments.ToList` statement modified but not the `departments = db.Departments` statement?</span></span> <span data-ttu-id="c7c99-142">Důvodem je, že pouze příkazy, které způsobují dotazy nebo příkazy, které se odesílají do databáze se provedl asynchronně.</span><span class="sxs-lookup"><span data-stu-id="c7c99-142">The reason is that only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="c7c99-143">`departments = db.Departments` Příkaz nastaví dotazu, ale není dotaz provést, dokud `ToList` metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="c7c99-143">The `departments = db.Departments` statement sets up a query but the query is not executed until the `ToList` method is called.</span></span> <span data-ttu-id="c7c99-144">Proto pouze `ToList` metoda provádí asynchronně.</span><span class="sxs-lookup"><span data-stu-id="c7c99-144">Therefore, only the `ToList` method is executed asynchronously.</span></span>

<span data-ttu-id="c7c99-145">V `Details` metoda a `HttpGet` `Edit` a `Delete` metody, `Find` metoda je ten, který způsobí, že dotaz k odeslání do databáze, tak, aby se metoda, která se provede asynchronně:</span><span class="sxs-lookup"><span data-stu-id="c7c99-145">In the `Details` method and the `HttpGet` `Edit` and `Delete` methods, the `Find` method is the one that causes a query to be sent to the database, so that's the method that gets executed asynchronously:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

<span data-ttu-id="c7c99-146">V `Create`, `HttpPost Edit`, a `DeleteConfirmed` metody, je `SaveChanges` volání metody, která způsobí, že příkaz má být proveden, nikoli příkazy, jako `db.Departments.Add(department)` entity což vedlo jenom v paměti, která má být upraven.</span><span class="sxs-lookup"><span data-stu-id="c7c99-146">In the `Create`, `HttpPost Edit`, and `DeleteConfirmed` methods, it is the `SaveChanges` method call that causes a command to be executed, not statements such as `db.Departments.Add(department)` which only cause entities in memory to be modified.</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

<span data-ttu-id="c7c99-147">Otevřít *Views\Department\Index.cshtml*a nahraďte kód šablony následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c7c99-147">Open *Views\Department\Index.cshtml*, and replace the template code with the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

<span data-ttu-id="c7c99-148">Tento kód změní název z indexu na oddělení, přesune jméno správce napravo a poskytuje úplné jméno správce.</span><span class="sxs-lookup"><span data-stu-id="c7c99-148">This code changes the title from Index to Departments, moves the Administrator name to the right, and provides the full name of the administrator.</span></span>

<span data-ttu-id="c7c99-149">V části Vytvoření, odstranění, podrobností a upravte zobrazení, změnit titulek `InstructorID` pole "Administrator" stejným způsobem jako v zobrazeních kurzu změníte pole název oddělení "Oddělení".</span><span class="sxs-lookup"><span data-stu-id="c7c99-149">In the Create, Delete, Details, and Edit views, change the caption for the `InstructorID` field to "Administrator" the same way you changed the department name field to "Department" in the Course views.</span></span>

<span data-ttu-id="c7c99-150">Zobrazení v vytvořit a upravit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c7c99-150">In the Create and Edit views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

<span data-ttu-id="c7c99-151">V zobrazení Delete a podrobnosti pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c7c99-151">In the Delete and Details views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

<span data-ttu-id="c7c99-152">Spusťte aplikaci a klikněte na tlačítko **oddělení** kartu.</span><span class="sxs-lookup"><span data-stu-id="c7c99-152">Run the application, and click the **Departments** tab.</span></span>

<span data-ttu-id="c7c99-153">Všechno, co funguje stejně jako v ostatních řadičů, ale v tomto kontroleru všechny dotazy SQL jsou asynchronně.</span><span class="sxs-lookup"><span data-stu-id="c7c99-153">Everything works the same as in the other controllers, but in this controller all of the SQL queries are executing asynchronously.</span></span>

<span data-ttu-id="c7c99-154">Je potřeba vědět při použití asynchronní programování s rozhraním Entity Framework pár věcí:</span><span class="sxs-lookup"><span data-stu-id="c7c99-154">Some things to be aware of when you are using asynchronous programming with the Entity Framework:</span></span>

- <span data-ttu-id="c7c99-155">Asynchronní kód není bezpečné pro vlákna.</span><span class="sxs-lookup"><span data-stu-id="c7c99-155">The async code is not thread safe.</span></span> <span data-ttu-id="c7c99-156">Jinými slovy jinými slovy, nedoporučujeme provádět více operací paralelně pomocí stejné instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="c7c99-156">In other words, in other words, don't try to do multiple operations in parallel using the same context instance.</span></span>
- <span data-ttu-id="c7c99-157">Pokud chcete využít výhod výkony těží z asynchronní kód, ujistěte se, že všechny knihovny balíčky, které používáte (například stránkování), asynchronní použijte i v případě volají všechny Entity Framework metody, které způsobují dotazů k odeslání do databáze.</span><span class="sxs-lookup"><span data-stu-id="c7c99-157">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

## <a name="use-stored-procedures"></a><span data-ttu-id="c7c99-158">Použití uložených procedur</span><span class="sxs-lookup"><span data-stu-id="c7c99-158">Use stored procedures</span></span>

<span data-ttu-id="c7c99-159">Některé vývojáře a specializující raději pomocí uložených procedur pro přístup k databázi.</span><span class="sxs-lookup"><span data-stu-id="c7c99-159">Some developers and DBAs prefer to use stored procedures for database access.</span></span> <span data-ttu-id="c7c99-160">V dřívějších verzích sady Entity Framework můžete načíst data pomocí uložené procedury pomocí [spuštění neupraveného dotazu SQL](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), ale nemůže dát pokyn EF pouze pomocí uložených procedur pro operace update.</span><span class="sxs-lookup"><span data-stu-id="c7c99-160">In earlier versions of Entity Framework you can retrieve data using a stored procedure by [executing a raw SQL query](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), but you can't instruct EF to use stored procedures for update operations.</span></span> <span data-ttu-id="c7c99-161">V EF 6 je snadno konfigurovatelné Code First pomocí uložených procedur.</span><span class="sxs-lookup"><span data-stu-id="c7c99-161">In EF 6 it's easy to configure Code First to use stored procedures.</span></span>

1. <span data-ttu-id="c7c99-162">V *DAL\SchoolContext.cs*, přidejte zvýrazněný kód, který `OnModelCreating` metody.</span><span class="sxs-lookup"><span data-stu-id="c7c99-162">In *DAL\SchoolContext.cs*, add the highlighted code to the `OnModelCreating` method.</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    <span data-ttu-id="c7c99-163">Tento kód nastaví Entity Frameworku na použití uložené procedury pro vložení, aktualizovat a odstraňovat operace `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="c7c99-163">This code instructs Entity Framework to use stored procedures for insert, update, and delete operations on the `Department` entity.</span></span>
2. <span data-ttu-id="c7c99-164">V konzole pro správu balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c7c99-164">In Package Manage Console, enter the following command:</span></span>

    `add-migration DepartmentSP`

    <span data-ttu-id="c7c99-165">Otevřít *migrace\&lt; časové razítko&gt;\_DepartmentSP.cs* zobrazíte kód `Up` metodu, která vytvoří Insert, Update a Delete uložené procedury:</span><span class="sxs-lookup"><span data-stu-id="c7c99-165">Open *Migrations\&lt;timestamp&gt;\_DepartmentSP.cs* to see the code in the `Up` method that creates Insert, Update, and Delete stored procedures:</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. <span data-ttu-id="c7c99-166">V konzole pro správu balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c7c99-166">In Package Manage Console, enter the following command:</span></span>

     `update-database`
4. <span data-ttu-id="c7c99-167">Spusťte aplikaci v režimu ladění, klikněte na tlačítko **oddělení** kartu a potom klikněte na tlačítko **vytvořit nový**.</span><span class="sxs-lookup"><span data-stu-id="c7c99-167">Run the application in debug mode, click the **Departments** tab, and then click **Create New**.</span></span>
5. <span data-ttu-id="c7c99-168">Zadejte data pro jiného oddělení a klikněte na **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c7c99-168">Enter data for a new department, and then click **Create**.</span></span>

6. <span data-ttu-id="c7c99-169">V sadě Visual Studio, podívejte se na protokoly v **výstup** okno a zobrazit, uložené procedury byla použita k vložit nový řádek oddělení.</span><span class="sxs-lookup"><span data-stu-id="c7c99-169">In Visual Studio, look at the logs in the **Output** window to see that a stored procedure was used to insert the new Department row.</span></span>

     ![Oddělení vložit SP](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

<span data-ttu-id="c7c99-171">Kód nejprve vytvoří výchozí uložené procedury názvy.</span><span class="sxs-lookup"><span data-stu-id="c7c99-171">Code First creates default stored procedure names.</span></span> <span data-ttu-id="c7c99-172">Pokud používáte existující databázi, můžete potřebovat k přizpůsobení názvy uložených postupů Chcete-li použít uložené procedury v databázi již definována.</span><span class="sxs-lookup"><span data-stu-id="c7c99-172">If you are using an existing database, you might need to customize the stored procedure names in order to use stored procedures already defined in the database.</span></span> <span data-ttu-id="c7c99-173">Informace o tom, jak to udělat, najdete v části [Entity Framework Code první Insert/Update/Delete uložené procedury](https://msdn.microsoft.com/data/dn468673).</span><span class="sxs-lookup"><span data-stu-id="c7c99-173">For information about how to do that, see [Entity Framework Code First Insert/Update/Delete Stored Procedures](https://msdn.microsoft.com/data/dn468673).</span></span>

<span data-ttu-id="c7c99-174">Pokud chcete přizpůsobit, co vygeneruje uložených procedur, můžete upravit automaticky generovaný kód pro přenesení `Up` metodu, která vytvoří uloženou proceduru.</span><span class="sxs-lookup"><span data-stu-id="c7c99-174">If you want to customize what generated stored procedures do, you can edit the scaffolded code for the migrations `Up` method that creates the stored procedure.</span></span> <span data-ttu-id="c7c99-175">Díky tomu vaše změny se projeví vždy, když, migrace je spuštěný a uplatní se na vaši produkční databázi když migrace spustí automaticky v produkčním prostředí po nasazení.</span><span class="sxs-lookup"><span data-stu-id="c7c99-175">That way your changes are reflected whenever that migration is run and will be applied to your production database when migrations runs automatically in production after deployment.</span></span>

<span data-ttu-id="c7c99-176">Pokud chcete změnit stávající úložnou proceduru, který byl vytvořen v předchozí migrace, můžete pomocí příkazu Add-migrace generovat prázdné migrace a ručně napsat kód, který volá [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) – metoda .</span><span class="sxs-lookup"><span data-stu-id="c7c99-176">If you want to change an existing stored procedure that was created in a previous migration, you can use the Add-Migration command to generate a blank migration, and then manually write code that calls the [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) method.</span></span>

## <a name="deploy-to-azure"></a><span data-ttu-id="c7c99-177">Nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="c7c99-177">Deploy to Azure</span></span>

<span data-ttu-id="c7c99-178">Tato část vyžaduje, abyste dokončili nepovinný **nasazení aplikace do Azure** tématu [Migration a nasazení](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) této série kurzů.</span><span class="sxs-lookup"><span data-stu-id="c7c99-178">This section requires you to have completed the optional **Deploying the app to Azure** section in the [Migrations and Deployment](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial of this series.</span></span> <span data-ttu-id="c7c99-179">Pokud jste měli migrace chyby, které jste vyřešili tak, že odstraníte databáze v projektu pro místní, tuto část přeskočte.</span><span class="sxs-lookup"><span data-stu-id="c7c99-179">If you had migrations errors that you resolved by deleting the database in your local project, skip this section.</span></span>

1. <span data-ttu-id="c7c99-180">Ve Visual Studiu klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** a vyberte **publikovat** v místní nabídce.</span><span class="sxs-lookup"><span data-stu-id="c7c99-180">In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.</span></span>
2. <span data-ttu-id="c7c99-181">Klikněte na tlačítko **publikovat**.</span><span class="sxs-lookup"><span data-stu-id="c7c99-181">Click **Publish**.</span></span>

    <span data-ttu-id="c7c99-182">Visual Studio nasadí aplikaci do Azure a aplikace se otevře ve výchozím prohlížeči, běží v Azure.</span><span class="sxs-lookup"><span data-stu-id="c7c99-182">Visual Studio deploys the application to Azure, and the application opens in your default browser, running in Azure.</span></span>
3. <span data-ttu-id="c7c99-183">Otestování aplikace ověří, že funguje.</span><span class="sxs-lookup"><span data-stu-id="c7c99-183">Test the application to verify it's working.</span></span>

    <span data-ttu-id="c7c99-184">Při prvním spuštění stránky, který přistupuje k databázi, Entity Framework provozovat všechny migrace `Up` metod požadovaných k aktuální s aktuálním datovým modelem databáze.</span><span class="sxs-lookup"><span data-stu-id="c7c99-184">The first time you run a page that accesses the database, the Entity Framework runs all of the migrations `Up` methods required to bring the database up to date with the current data model.</span></span> <span data-ttu-id="c7c99-185">Teď můžete použít všechny webové stránky, které jste přidali od posledního, které jste nasadili, včetně oddělení stránek, které jste přidali v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c7c99-185">You can now use all of the web pages that you added since the last time you deployed, including the Department pages that you added in this tutorial.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="c7c99-186">Získat kód</span><span class="sxs-lookup"><span data-stu-id="c7c99-186">Get the code</span></span>

[<span data-ttu-id="c7c99-187">Stáhnout dokončený projekt</span><span class="sxs-lookup"><span data-stu-id="c7c99-187">Download the Completed Project</span></span>](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a><span data-ttu-id="c7c99-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c7c99-188">Additional resources</span></span>

<span data-ttu-id="c7c99-189">Odkazy na další zdroje Entity Framework najdete v [přístup k datům ASP.NET – doporučené zdroje informací](../../../../whitepapers/aspnet-data-access-content-map.md).</span><span class="sxs-lookup"><span data-stu-id="c7c99-189">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c7c99-190">Další kroky</span><span class="sxs-lookup"><span data-stu-id="c7c99-190">Next steps</span></span>

<span data-ttu-id="c7c99-191">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="c7c99-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c7c99-192">Dozvěděli jste se o asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="c7c99-192">Learned about asynchronous code</span></span>
> * <span data-ttu-id="c7c99-193">Vytvoří kontroler oddělení</span><span class="sxs-lookup"><span data-stu-id="c7c99-193">Created a Department controller</span></span>
> * <span data-ttu-id="c7c99-194">Používat uložené procedury</span><span class="sxs-lookup"><span data-stu-id="c7c99-194">Used stored procedures</span></span>
> * <span data-ttu-id="c7c99-195">Nasazení do Azure</span><span class="sxs-lookup"><span data-stu-id="c7c99-195">Deployed to Azure</span></span>

<span data-ttu-id="c7c99-196">Přejděte k dalším článku se naučíte, jak řešit konflikty při více uživatelů aktualizovat stejná entita ve stejnou dobu.</span><span class="sxs-lookup"><span data-stu-id="c7c99-196">Advance to the next article to learn how to handle conflicts when multiple users update the same entity at the same time.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="c7c99-197">Ošetření souběžnosti</span><span class="sxs-lookup"><span data-stu-id="c7c99-197">Handling concurrency</span></span>](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
