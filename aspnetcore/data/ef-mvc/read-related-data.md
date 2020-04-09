---
title: 'Kurz: Čtení souvisejících dat - ASP.NET MVC s EF Core'
description: V tomto kurzu budete číst a zobrazovat související data -- to znamená data, která rozhraní entity načte do navigačních vlastností.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: a6e63723101ab09219db81ee9796c3938a612226
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657106"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="8afa7-103">Kurz: Čtení souvisejících dat - ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="8afa7-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="8afa7-104">V předchozím kurzu jste dokončili datový model školy.</span><span class="sxs-lookup"><span data-stu-id="8afa7-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="8afa7-105">V tomto kurzu budete číst a zobrazovat související data -- to znamená data, která rozhraní entity načte do navigačních vlastností.</span><span class="sxs-lookup"><span data-stu-id="8afa7-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="8afa7-106">Na následujících obrázcích jsou uvedeny stránky, se kterými budete pracovat.</span><span class="sxs-lookup"><span data-stu-id="8afa7-106">The following illustrations show the pages that you'll work with.</span></span>

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)

<span data-ttu-id="8afa7-109">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="8afa7-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8afa7-110">Přečtěte si, jak načíst související data</span><span class="sxs-lookup"><span data-stu-id="8afa7-110">Learn how to load related data</span></span>
> * <span data-ttu-id="8afa7-111">Vytvoření stránky Kurzů</span><span class="sxs-lookup"><span data-stu-id="8afa7-111">Create a Courses page</span></span>
> * <span data-ttu-id="8afa7-112">Vytvoření stránky Instruktoři</span><span class="sxs-lookup"><span data-stu-id="8afa7-112">Create an Instructors page</span></span>
> * <span data-ttu-id="8afa7-113">Informace o explicitním načítání</span><span class="sxs-lookup"><span data-stu-id="8afa7-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8afa7-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8afa7-114">Prerequisites</span></span>

* [<span data-ttu-id="8afa7-115">Vytvoření složitého datového modelu</span><span class="sxs-lookup"><span data-stu-id="8afa7-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="8afa7-116">Přečtěte si, jak načíst související data</span><span class="sxs-lookup"><span data-stu-id="8afa7-116">Learn how to load related data</span></span>

<span data-ttu-id="8afa7-117">Existuje několik způsobů, jak software orm (Object-Relational Mapping), jako je entity Framework, může načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="8afa7-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="8afa7-118">Dychtivé nakládání.</span><span class="sxs-lookup"><span data-stu-id="8afa7-118">Eager loading.</span></span> <span data-ttu-id="8afa7-119">Při čtení entity se spolu s ní načítají související data.</span><span class="sxs-lookup"><span data-stu-id="8afa7-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="8afa7-120">To obvykle vede k dotazu na jedno spojení, který načte všechna data, která je potřeba.</span><span class="sxs-lookup"><span data-stu-id="8afa7-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="8afa7-121">Můžete zadat dychtivé načítání v `Include` core `ThenInclude` entity framework pomocí a metody.</span><span class="sxs-lookup"><span data-stu-id="8afa7-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad dychtivénačítání](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="8afa7-123">Můžete načíst některá data v samostatných dotazech a EF "opravuje" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8afa7-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="8afa7-124">To znamená EF automaticky přidá samostatně načtené entity, kde patří do navigačních vlastností dříve načtených entit.</span><span class="sxs-lookup"><span data-stu-id="8afa7-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="8afa7-125">Pro dotaz, který načítá související data, můžete použít metodu `Load` namísto metody, `ToList` `Single`která vrací seznam nebo objekt, například nebo .</span><span class="sxs-lookup"><span data-stu-id="8afa7-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="8afa7-127">Explicitní načítání.</span><span class="sxs-lookup"><span data-stu-id="8afa7-127">Explicit loading.</span></span> <span data-ttu-id="8afa7-128">Při prvním čtení entity se nenačtou související data.</span><span class="sxs-lookup"><span data-stu-id="8afa7-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8afa7-129">Napíšete kód, který načte související data, pokud je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="8afa7-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="8afa7-130">Stejně jako v případě dychtivé načítání se samostatnými dotazy, explicitní načítání výsledky ve více dotazů odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="8afa7-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="8afa7-131">Rozdíl je, že s explicitní načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="8afa7-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="8afa7-132">V entity Framework Core 1.1 `Load` můžete použít metodu k explicitnínačítání.</span><span class="sxs-lookup"><span data-stu-id="8afa7-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="8afa7-133">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8afa7-133">For example:</span></span>

  ![Příklad explicitního načítání](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="8afa7-135">Opožděné načítání.</span><span class="sxs-lookup"><span data-stu-id="8afa7-135">Lazy loading.</span></span> <span data-ttu-id="8afa7-136">Při prvním čtení entity se nenačtou související data.</span><span class="sxs-lookup"><span data-stu-id="8afa7-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8afa7-137">Při prvním pokusu o přístup k navigační vlastnosti jsou však automaticky načtena data požadovaná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="8afa7-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="8afa7-138">Dotaz je odeslán do databáze pokaždé, když se pokusíte získat data z navigační vlastnosti poprvé.</span><span class="sxs-lookup"><span data-stu-id="8afa7-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="8afa7-139">Entity Framework Core 1.0 nepodporuje opožděné načítání.</span><span class="sxs-lookup"><span data-stu-id="8afa7-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="8afa7-140">Otázky výkonu</span><span class="sxs-lookup"><span data-stu-id="8afa7-140">Performance considerations</span></span>

<span data-ttu-id="8afa7-141">Pokud víte, že potřebujete související data pro každou načtenou entitu, eager loading často nabízí nejlepší výkon, protože jeden dotaz odeslaný do databáze je obvykle efektivnější než samostatné dotazy pro každou načtenou entitu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="8afa7-142">Předpokládejme například, že každé oddělení má deset souvisejících kurzů.</span><span class="sxs-lookup"><span data-stu-id="8afa7-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="8afa7-143">Dychtivé načítání všech souvisejících dat by mělo za následek pouze jeden dotaz (spojení) a jednu odezvu do databáze.</span><span class="sxs-lookup"><span data-stu-id="8afa7-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="8afa7-144">Samostatný dotaz na kurzy pro každé oddělení by měl za následek jedenáct zpátečních cest do databáze.</span><span class="sxs-lookup"><span data-stu-id="8afa7-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="8afa7-145">Extra zpáteční cesty do databáze jsou obzvláště škodlivé pro výkon, když je vysoká latence.</span><span class="sxs-lookup"><span data-stu-id="8afa7-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="8afa7-146">Na druhou stranu v některých scénářích samostatné dotazy je efektivnější.</span><span class="sxs-lookup"><span data-stu-id="8afa7-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="8afa7-147">Eager načítání všech souvisejících dat v jednom dotazu může způsobit velmi složité spojení, které mají být generovány, které SQL Server nelze efektivně zpracovat.</span><span class="sxs-lookup"><span data-stu-id="8afa7-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="8afa7-148">Nebo pokud potřebujete přístup k navigačním vlastnostem entity pouze pro podmnožinu sady entit, které zpracováváte, samostatné dotazy mohou fungovat lépe, protože dychtivé načítání všeho předem by načetlo více dat, než potřebujete.</span><span class="sxs-lookup"><span data-stu-id="8afa7-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="8afa7-149">Pokud je výkon kritický, je nejlepší otestovat výkon oběma směry, aby bylo možné provést nejlepší volbu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="8afa7-150">Vytvoření stránky Kurzů</span><span class="sxs-lookup"><span data-stu-id="8afa7-150">Create a Courses page</span></span>

<span data-ttu-id="8afa7-151">Entita Kurz obsahuje navigační vlastnost, která obsahuje entitu oddělení oddělení, ke kterému je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="8afa7-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="8afa7-152">Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů, musíte získat vlastnost Name z `Course.Department` entity Oddělení, která je ve vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="8afa7-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="8afa7-153">Vytvořte řadič s názvem CoursesController pro typ entity kurzu, pomocí stejných možností pro **MVC řadič se zobrazeními, pomocí** entity framework scaffolder, které jste dříve pro řadič Students, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="8afa7-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Ovladač přidat kurzy](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="8afa7-155">Otevřete *CoursesController.cs* `Index` a zkontrolujte metodu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="8afa7-156">Automatické generování uživatelského čípku určileager `Department` zatížení pro `Include` navigační vlastnost pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="8afa7-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="8afa7-157">`Index` Nahraďte metodu následujícím kódem, který `IQueryable` používá vhodnější název`courses` pro `schoolContext`entity, které vrací entity kurzu ( místo ):</span><span class="sxs-lookup"><span data-stu-id="8afa7-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="8afa7-158">Otevřete *zobrazení/kurzy/index.cshtml* a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="8afa7-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="8afa7-159">Změny jsou zvýrazněny:</span><span class="sxs-lookup"><span data-stu-id="8afa7-159">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="8afa7-160">V kódu slepeného kódu jste provedli následující změny:</span><span class="sxs-lookup"><span data-stu-id="8afa7-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="8afa7-161">Nadpis byl změněn z rejstříku na Kurzy.</span><span class="sxs-lookup"><span data-stu-id="8afa7-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="8afa7-162">Byl přidán sloupec **Číslo,** který zobrazuje hodnotu vlastnosti. `CourseID`</span><span class="sxs-lookup"><span data-stu-id="8afa7-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="8afa7-163">Ve výchozím nastavení nejsou primární klíče skládané, protože obvykle nemají pro koncové uživatele smysl.</span><span class="sxs-lookup"><span data-stu-id="8afa7-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="8afa7-164">V tomto případě je však primární klíč smysluplný a chcete jej zobrazit.</span><span class="sxs-lookup"><span data-stu-id="8afa7-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="8afa7-165">Sloupec **Oddělení** se změnil tak, aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="8afa7-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="8afa7-166">Kód zobrazuje `Name` vlastnost entity Oddělení, která je `Department` načtena do vlastnosti navigace:</span><span class="sxs-lookup"><span data-stu-id="8afa7-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="8afa7-167">Spusťte aplikaci a vyberte kartu **Kurzy,** abyste viděli seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="8afa7-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="8afa7-169">Vytvoření stránky Instruktoři</span><span class="sxs-lookup"><span data-stu-id="8afa7-169">Create an Instructors page</span></span>

<span data-ttu-id="8afa7-170">V této části vytvoříte řadič a zobrazení pro entitu Instruktor, abyste zobrazili stránku Instruktoři:</span><span class="sxs-lookup"><span data-stu-id="8afa7-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)

<span data-ttu-id="8afa7-172">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="8afa7-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="8afa7-173">Seznam instruktorů zobrazuje související data z entity OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="8afa7-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="8afa7-174">Entity Instruktor a OfficeAssignment jsou ve vztahu 1:Nula nebo jedna.</span><span class="sxs-lookup"><span data-stu-id="8afa7-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="8afa7-175">Budete používat dychtivé načítání pro officeassignment entity.</span><span class="sxs-lookup"><span data-stu-id="8afa7-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="8afa7-176">Jak bylo vysvětleno dříve, eager načítání je obvykle efektivnější, když potřebujete související data pro všechny načtené řádky primární tabulky.</span><span class="sxs-lookup"><span data-stu-id="8afa7-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="8afa7-177">V takovém případě chcete zobrazit přiřazení kanceláře pro všechny zobrazené instruktory.</span><span class="sxs-lookup"><span data-stu-id="8afa7-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="8afa7-178">Když uživatel vybere instruktora, zobrazí se související entity kurzu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="8afa7-179">Instruktor a samozřejmě subjekty jsou ve vztahu n-to-n.</span><span class="sxs-lookup"><span data-stu-id="8afa7-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="8afa7-180">Budete používat dychtivé načítání pro entity kurzu a jejich související entity oddělení.</span><span class="sxs-lookup"><span data-stu-id="8afa7-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="8afa7-181">V takovém případě mohou být samostatné dotazy efektivnější, protože kurzy potřebujete pouze pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="8afa7-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="8afa7-182">Tento příklad však ukazuje, jak použít dychtivé načítání pro navigační vlastnosti v rámci entit, které jsou samy v navigačnívlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8afa7-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="8afa7-183">Když uživatel vybere kurz, zobrazí se související data ze sady entit Zápisy.</span><span class="sxs-lookup"><span data-stu-id="8afa7-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="8afa7-184">Entity Kurz a Zápis jsou ve vztahu 1:N.</span><span class="sxs-lookup"><span data-stu-id="8afa7-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="8afa7-185">Budete používat samostatné dotazy pro entity zápisu a jejich související studentské entity.</span><span class="sxs-lookup"><span data-stu-id="8afa7-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="8afa7-186">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="8afa7-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="8afa7-187">Stránka Instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="8afa7-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="8afa7-188">Proto vytvoříte model zobrazení, který obsahuje tři vlastnosti, z nichž každá obsahuje data pro jednu z tabulek.</span><span class="sxs-lookup"><span data-stu-id="8afa7-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="8afa7-189">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8afa7-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="8afa7-190">Vytvoření ovladače instruktora a zobrazení</span><span class="sxs-lookup"><span data-stu-id="8afa7-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="8afa7-191">Vytvořte řadič instruktorů s akcemi ef pro čtení a zápis, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="8afa7-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Přidat ovladač Instruktoři](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="8afa7-193">Otevřete *InstructorsController.cs* a přidejte příkaz using pro obor názvů ViewModels:</span><span class="sxs-lookup"><span data-stu-id="8afa7-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="8afa7-194">Nahraďte metodu Index následujícím kódem, který probíhá dychtivé načítání souvisejících dat, a vložte je do modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8afa7-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="8afa7-195">Metoda přijímá volitelná data`id`trasy ( )`courseID`a parametr řetězce dotazu ( ), který poskytuje hodnoty ID vybraného instruktora a vybraného kurzu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="8afa7-196">Parametry jsou poskytovány hypertextovými odkazy **Select** na stránce.</span><span class="sxs-lookup"><span data-stu-id="8afa7-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="8afa7-197">Kód začíná vytvořením instance modelu zobrazení a vložením seznamu instruktorů.</span><span class="sxs-lookup"><span data-stu-id="8afa7-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="8afa7-198">Kód určuje nedočkavé načítání vlastností `Instructor.OfficeAssignment` a `Instructor.CourseAssignments` navigace.</span><span class="sxs-lookup"><span data-stu-id="8afa7-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="8afa7-199">V `CourseAssignments` rámci vlastnosti je `Course` načtena vlastnost `Enrollments` a `Department` v rámci toho `Enrollment` jsou `Student` načteny vlastnosti a v rámci každé entity je načtena vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8afa7-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="8afa7-200">Vzhledem k tomu, že zobrazení vždy vyžaduje entitu OfficeAssignment, je efektivnější načíst ve stejném dotazu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="8afa7-201">Entity kurzu jsou vyžadovány, když je na webové stránce vybrán instruktor, takže jeden dotaz je lepší než více dotazů pouze v případě, že se stránka zobrazuje častěji s vybraným kurzem než bez.</span><span class="sxs-lookup"><span data-stu-id="8afa7-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="8afa7-202">Kód se `CourseAssignments` opakuje `Course` a protože potřebujete `Course`dvě vlastnosti z .</span><span class="sxs-lookup"><span data-stu-id="8afa7-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="8afa7-203">První řetězec `ThenInclude` volání `CourseAssignment.Course`získá `Course.Enrollments`, `Enrollment.Student`a .</span><span class="sxs-lookup"><span data-stu-id="8afa7-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="8afa7-204">V tomto okamžiku v `ThenInclude` kódu jiný by `Student`pro navigační vlastnosti , které nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="8afa7-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="8afa7-205">Volání `Include` však `Instructor` začíná znovu vlastnostmi, takže musíte znovu projít `Course.Department` řetězem, tentokrát zadáním místo `Course.Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="8afa7-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="8afa7-206">Následující kód se spustí, když byl vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="8afa7-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="8afa7-207">Vybraný instruktor je načten ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8afa7-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="8afa7-208">`Courses` Vlastnost modelu zobrazení je pak načtena s entity course `CourseAssignments` z navigační vlastnosti tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="8afa7-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="8afa7-209">Metoda `Where` vrátí kolekci, ale v tomto případě kritéria předaná této metodě za následek pouze jeden instruktor entity vrácena.</span><span class="sxs-lookup"><span data-stu-id="8afa7-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="8afa7-210">Metoda `Single` převede kolekci na jednu entitu Instruktor, která `CourseAssignments` umožňuje přístup k vlastnosti této entity.</span><span class="sxs-lookup"><span data-stu-id="8afa7-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="8afa7-211">Vlastnost `CourseAssignments` obsahuje `CourseAssignment` entity, ze kterých chcete `Course` pouze související entity.</span><span class="sxs-lookup"><span data-stu-id="8afa7-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="8afa7-212">Metodu `Single` použijete v kolekci, když víte, že kolekce bude mít pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="8afa7-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="8afa7-213">Single Metoda vyvolá výjimku, pokud kolekce předána je prázdný nebo pokud existuje více než jednu položku.</span><span class="sxs-lookup"><span data-stu-id="8afa7-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="8afa7-214">Alternativou `SingleOrDefault`je , která vrátí výchozí hodnotu (null v tomto případě), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="8afa7-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="8afa7-215">V tomto případě by však stále vést k výjimce (z pokusu `Courses` o nalezení vlastnosti na odkaz null) a zpráva o výjimce by méně jasně uvést příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="8afa7-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="8afa7-216">Při volání `Single` metody můžete také předat v Where podmínku `Where` namísto volání metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="8afa7-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="8afa7-217">Namísto:</span><span class="sxs-lookup"><span data-stu-id="8afa7-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="8afa7-218">Dále, pokud byl kurz vybrán, vybraný kurz je načten ze seznamu kurzů v modelu pohledu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="8afa7-219">Potom `Enrollments` je vlastnost modelu zobrazení načtena s entity Zápisu `Enrollments` z navigační vlastnosti tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="8afa7-220">Změna zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="8afa7-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="8afa7-221">V *části Zobrazení/Instruktoři/Index.cshtml*nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="8afa7-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="8afa7-222">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="8afa7-222">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="8afa7-223">V existujícím kódu jste provedli následující změny:</span><span class="sxs-lookup"><span data-stu-id="8afa7-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="8afa7-224">Třídu modelu `InstructorIndexData`byla změněna na .</span><span class="sxs-lookup"><span data-stu-id="8afa7-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="8afa7-225">Název stránky byl změněn z **Rejstříku** na **Instruktoři**.</span><span class="sxs-lookup"><span data-stu-id="8afa7-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="8afa7-226">Přidán sloupec **Office,** který se zobrazí `item.OfficeAssignment.Location` pouze v případě, `item.OfficeAssignment` že nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="8afa7-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="8afa7-227">(Vzhledem k tomu, že se jedná o vztah 1:Nula nebo jeden, nemusí existovat související entita OfficeAssignment.)</span><span class="sxs-lookup"><span data-stu-id="8afa7-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="8afa7-228">Byl přidán sloupec **Kurzy,** který zobrazuje kurzy vyučované jednotlivými instruktory.</span><span class="sxs-lookup"><span data-stu-id="8afa7-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="8afa7-229">Další informace naleznete v části [Explicitní přechod řádku](xref:mvc/views/razor#explicit-line-transition) v článku syntaxe razor.</span><span class="sxs-lookup"><span data-stu-id="8afa7-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="8afa7-230">Přidán kód, který `class="success"` dynamicky přidává do `tr` prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="8afa7-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="8afa7-231">Tím nastavíte barvu pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="8afa7-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="8afa7-232">Byl přidán nový hypertextový odkaz s názvem **Vybrat** bezprostředně před ostatními odkazy v každém řádku, což způsobí, že id vybraného instruktora bude odesláno metodě. `Index`</span><span class="sxs-lookup"><span data-stu-id="8afa7-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="8afa7-233">Spusťte aplikaci a vyberte kartu **Instruktoři.** Na stránce se zobrazí vlastnost Umístění souvisejících entit OfficeAssignment a prázdná buňka tabulky, pokud neexistuje žádná související entita OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="8afa7-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Stránka Index instruktorů není vybrána.](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="8afa7-235">V souboru *Zobrazení/Instruktoři/Index.cshtml* za prvek uzavírací tabulky (na konci souboru) přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="8afa7-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="8afa7-236">Tento kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="8afa7-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="8afa7-237">Tento kód `Courses` přečte vlastnost modelu zobrazení pro zobrazení seznamu kurzů.</span><span class="sxs-lookup"><span data-stu-id="8afa7-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="8afa7-238">Poskytuje také **select** hypertextový odkaz, který odešle ID vybraného kurzu na metodu `Index` akce.</span><span class="sxs-lookup"><span data-stu-id="8afa7-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="8afa7-239">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="8afa7-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="8afa7-240">Nyní se zobrazí mřížka, která zobrazuje kurzy přiřazené vybranému instruktorovi, a pro každý kurz se zobrazí název přiřazeného oddělení.</span><span class="sxs-lookup"><span data-stu-id="8afa7-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Vybraná stránka instruktorů instruktorů instruktorů instruktorů instruktorů instruktorů instruktorů instruktorů instruktor](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="8afa7-242">Po bloku kódu, který jste právě přidali, přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="8afa7-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="8afa7-243">Zobrazí se seznam studentů, kteří jsou zapsáni do kurzu, když je tento kurz vybrán.</span><span class="sxs-lookup"><span data-stu-id="8afa7-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="8afa7-244">Tento kód přečte Vlastnost Enrollments modelu zobrazení, aby se zobrazil seznam studentů zapsaných v kurzu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="8afa7-245">Znovu aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="8afa7-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="8afa7-246">Poté vyberte kurz, který zobrazí seznam zapsaných studentů a jejich známky.</span><span class="sxs-lookup"><span data-stu-id="8afa7-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Instruktoři Index stránky instruktor a kurz vybrán](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="8afa7-248">O explicitním načítání</span><span class="sxs-lookup"><span data-stu-id="8afa7-248">About explicit loading</span></span>

<span data-ttu-id="8afa7-249">Při načtení seznamu instruktorů v *InstructorsController.cs*, jste `CourseAssignments` zadali dychtivé načítání pro navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8afa7-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="8afa7-250">Předpokládejme, že jste očekávali, že uživatelé budou jen zřídka chtít zobrazit zápisy do vybraného instruktora a kurzu.</span><span class="sxs-lookup"><span data-stu-id="8afa7-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="8afa7-251">V takovém případě můžete chtít načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="8afa7-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="8afa7-252">Chcete-li zobrazit příklad, jak provést `Index` explicitní načítání, nahraďte metodu následujícím kódem, který odebere dychtivé načítání pro registrace a načte tuto vlastnost explicitně.</span><span class="sxs-lookup"><span data-stu-id="8afa7-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="8afa7-253">Změny kódu jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="8afa7-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="8afa7-254">Nový kód klesne *ThenInclude* volání metody pro zápis dat z kódu, který načítá entity instruktora.</span><span class="sxs-lookup"><span data-stu-id="8afa7-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="8afa7-255">To také `AsNoTracking`kapky .</span><span class="sxs-lookup"><span data-stu-id="8afa7-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="8afa7-256">Pokud je vybrán instruktor a kurz, zvýrazněný kód načte entity Zápisu pro vybraný kurz a entity studenta pro každou registraci.</span><span class="sxs-lookup"><span data-stu-id="8afa7-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="8afa7-257">Spusťte aplikaci, přejděte na stránku Index instruktorů a neuvidíte žádný rozdíl v tom, co se na stránce zobrazuje, i když jste změnili způsob načítání dat.</span><span class="sxs-lookup"><span data-stu-id="8afa7-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="8afa7-258">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="8afa7-258">Get the code</span></span>

[<span data-ttu-id="8afa7-259">Stáhněte nebo zobrazte dokončenou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8afa7-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="8afa7-260">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8afa7-260">Next steps</span></span>

<span data-ttu-id="8afa7-261">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="8afa7-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8afa7-262">Zjistili, jak načíst související data</span><span class="sxs-lookup"><span data-stu-id="8afa7-262">Learned how to load related data</span></span>
> * <span data-ttu-id="8afa7-263">Stránka Vytvoření kurzů</span><span class="sxs-lookup"><span data-stu-id="8afa7-263">Created a Courses page</span></span>
> * <span data-ttu-id="8afa7-264">Vytvoření stránky Instruktoři</span><span class="sxs-lookup"><span data-stu-id="8afa7-264">Created an Instructors page</span></span>
> * <span data-ttu-id="8afa7-265">Informace o explicitním načítání</span><span class="sxs-lookup"><span data-stu-id="8afa7-265">Learned about explicit loading</span></span>

<span data-ttu-id="8afa7-266">Přejdete k dalšímu kurzu, kde se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="8afa7-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8afa7-267">Aktualizace souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="8afa7-267">Update related data</span></span>](update-related-data.md)
