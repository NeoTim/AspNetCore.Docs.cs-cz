---
title: 'Kurz: Čtení souvisejících dat – ASP.NET MVC s EF Core'
description: V tomto kurzu budete čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.
author: rick-anderson
ms.author: tdykstra
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 5b45ced4fe6ed220c9a63bc6480c8c8520b36ed4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900360"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="8a142-103">Kurz: Čtení souvisejících dat – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="8a142-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="8a142-104">V předchozím kurzu jste dokončili školní datového modelu.</span><span class="sxs-lookup"><span data-stu-id="8a142-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="8a142-105">V tomto kurzu budete čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8a142-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="8a142-106">Na následujících obrázcích stránky, kterou budete pracovat.</span><span class="sxs-lookup"><span data-stu-id="8a142-106">The following illustrations show the pages that you'll work with.</span></span>

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)

<span data-ttu-id="8a142-109">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="8a142-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8a142-110">Zjistěte, jak načíst související data</span><span class="sxs-lookup"><span data-stu-id="8a142-110">Learn how to load related data</span></span>
> * <span data-ttu-id="8a142-111">Vytvoření stránky kurzy</span><span class="sxs-lookup"><span data-stu-id="8a142-111">Create a Courses page</span></span>
> * <span data-ttu-id="8a142-112">Vytvoření stránky Instruktoři</span><span class="sxs-lookup"><span data-stu-id="8a142-112">Create an Instructors page</span></span>
> * <span data-ttu-id="8a142-113">Další informace o explicitní načtení</span><span class="sxs-lookup"><span data-stu-id="8a142-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8a142-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8a142-114">Prerequisites</span></span>

* [<span data-ttu-id="8a142-115">Vytvoření složitého datového modelu</span><span class="sxs-lookup"><span data-stu-id="8a142-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="8a142-116">Zjistěte, jak načíst související data</span><span class="sxs-lookup"><span data-stu-id="8a142-116">Learn how to load related data</span></span>

<span data-ttu-id="8a142-117">Existuje několik způsobů objektově-relační mapování (ORM) určené softwaru, jako je rozhraní Entity Framework mohou načíst související data do navigační vlastnosti entity:</span><span class="sxs-lookup"><span data-stu-id="8a142-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="8a142-118">Předběžné načítání.</span><span class="sxs-lookup"><span data-stu-id="8a142-118">Eager loading.</span></span> <span data-ttu-id="8a142-119">Při čtení entity související data načíst společně.</span><span class="sxs-lookup"><span data-stu-id="8a142-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="8a142-120">Obvykle v důsledku jednoho spojení dotaz, který zkopíruje všechna data, který je nezbytný.</span><span class="sxs-lookup"><span data-stu-id="8a142-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="8a142-121">Předběžné načítání zadáte v Entity Framework Core pomocí `Include` a `ThenInclude` metody.</span><span class="sxs-lookup"><span data-stu-id="8a142-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Předběžné načítání příklad](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="8a142-123">Můžete načíst některá data v samostatné dotazy a EF termín "opravy" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8a142-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="8a142-124">To znamená EF automaticky přidá samostatně načtený entity, které patří, kde ve vlastnosti navigace entit dříve načtená.</span><span class="sxs-lookup"><span data-stu-id="8a142-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="8a142-125">Pro dotaz, který načte související data, můžete použít `Load` metoda místo metodu, která vrátí seznam nebo objekt, jako například `ToList` nebo `Single`.</span><span class="sxs-lookup"><span data-stu-id="8a142-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Příklad samostatné dotazy](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="8a142-127">Explicitní načtení.</span><span class="sxs-lookup"><span data-stu-id="8a142-127">Explicit loading.</span></span> <span data-ttu-id="8a142-128">Pokud entita je nejdřív přečíst, související data nebude načten.</span><span class="sxs-lookup"><span data-stu-id="8a142-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8a142-129">Můžete napsat kód, který načte související data, pokud je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="8a142-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="8a142-130">Stejně jako v případě eager načítání s samostatné dotazy explicitní načítání více dotazů za následek odeslán do databáze.</span><span class="sxs-lookup"><span data-stu-id="8a142-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="8a142-131">Rozdíl je, že s explicitní načtení, určuje kód navigačních vlastností, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="8a142-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="8a142-132">V Entity Framework Core 1.1 můžete použít `Load` metodu explicitní načtení.</span><span class="sxs-lookup"><span data-stu-id="8a142-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="8a142-133">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8a142-133">For example:</span></span>

  ![Příklad explicitní načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="8a142-135">Opožděné načtení.</span><span class="sxs-lookup"><span data-stu-id="8a142-135">Lazy loading.</span></span> <span data-ttu-id="8a142-136">Pokud entita je nejdřív přečíst, související data nebude načten.</span><span class="sxs-lookup"><span data-stu-id="8a142-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="8a142-137">Ale při prvním pokusu o přístup k vlastnosti navigace data požadovaná pro tuto navigační vlastnost je automaticky načte.</span><span class="sxs-lookup"><span data-stu-id="8a142-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="8a142-138">Bude odeslán dotaz do databáze při každém pokusu o získání dat z navigační vlastnost poprvé.</span><span class="sxs-lookup"><span data-stu-id="8a142-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="8a142-139">Entity Framework Core 1.0 nepodporuje opožděné načtení.</span><span class="sxs-lookup"><span data-stu-id="8a142-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="8a142-140">Důležité informace o výkonu</span><span class="sxs-lookup"><span data-stu-id="8a142-140">Performance considerations</span></span>

<span data-ttu-id="8a142-141">Pokud víte, že budete potřebovat pro každou entitu načíst související data, nemůžou dočkat, až načítání často nabízí nejlepší výkon, protože jednoho dotazu odeslaného do databáze je obvykle mnohem efektivnější než samostatné dotazy pro každou entitu načíst.</span><span class="sxs-lookup"><span data-stu-id="8a142-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="8a142-142">Předpokládejme například, že každé oddělení má deset související kurzy.</span><span class="sxs-lookup"><span data-stu-id="8a142-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="8a142-143">Předběžné načítání všechna související data výsledkem budou pouze jednou (spojení) dotazu a jedinou odezvy k databázi.</span><span class="sxs-lookup"><span data-stu-id="8a142-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="8a142-144">Samostatný dotaz kurzy pro každé oddělení způsobí jedenáct zpátečních cest k databázi.</span><span class="sxs-lookup"><span data-stu-id="8a142-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="8a142-145">Další zpátečních cest k databázi jsou zvlášť ztráty výkonu, když má vysokou latenci.</span><span class="sxs-lookup"><span data-stu-id="8a142-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="8a142-146">Na druhé straně v některých případech je efektivnější samostatné dotazy.</span><span class="sxs-lookup"><span data-stu-id="8a142-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="8a142-147">Předběžné načítání všechna související data v jednom dotazu může vést k velmi složité spojení být vytvořen, kterou SQL Server nemůže zpracovat efektivně.</span><span class="sxs-lookup"><span data-stu-id="8a142-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="8a142-148">Nebo pokud potřebujete přístup k entity navigační vlastnosti pouze pro dílčí sadu entit, které jste zpracování, samostatné dotazy může lépe provést, protože nemůžou dočkat, až načítání všechno, co ještě před zahájením by byl načten více dat, než potřebujete.</span><span class="sxs-lookup"><span data-stu-id="8a142-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="8a142-149">Pokud je nejdůležitější výkon, je nejvhodnější pro testování výkonu oba způsoby, aby bylo možné správně se rozhodnout.</span><span class="sxs-lookup"><span data-stu-id="8a142-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="8a142-150">Vytvoření stránky kurzy</span><span class="sxs-lookup"><span data-stu-id="8a142-150">Create a Courses page</span></span>

<span data-ttu-id="8a142-151">Kurz entita obsahuje navigační vlastnost obsahující tuto entitu oddělení oddělení, která je přiřazena kurzu.</span><span class="sxs-lookup"><span data-stu-id="8a142-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="8a142-152">Chcete-li zobrazit název přiřazený oddělení v seznamu kurzů, je potřeba získat název vlastnosti z oddělení entity, která je v `Course.Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8a142-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="8a142-153">Vytvořit řadič CoursesController pro typ entity kurz pomocí stejných možností pro **kontroler MVC se zobrazeními, s použitím Entity Framework** generátor, který jste provedli dříve pro studenty řadič, jak je znázorněno Následující obrázek:</span><span class="sxs-lookup"><span data-stu-id="8a142-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Přidat kontroler kurzy](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="8a142-155">Otevřít *CoursesController.cs* a prozkoumejte `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="8a142-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="8a142-156">Automatické generování uživatelského rozhraní má zadanou předběžné načítání pro `Department` navigační vlastnost s použitím `Include` metody.</span><span class="sxs-lookup"><span data-stu-id="8a142-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="8a142-157">Nahradit `Index` metodu s následujícím kódem, který používá více vhodný název pro `IQueryable` , který vrací kurz entity (`courses` místo `schoolContext`):</span><span class="sxs-lookup"><span data-stu-id="8a142-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="8a142-158">Otevřít *Views/Courses/Index.cshtml* a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="8a142-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="8a142-159">Změny jsou zvýrazněny:</span><span class="sxs-lookup"><span data-stu-id="8a142-159">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="8a142-160">Automaticky generovaný kód, které jste udělali následující změny:</span><span class="sxs-lookup"><span data-stu-id="8a142-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="8a142-161">Změnit záhlaví od indexu ke kurzům.</span><span class="sxs-lookup"><span data-stu-id="8a142-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="8a142-162">Přidá **číslo** sloupec, který ukazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8a142-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="8a142-163">Ve výchozím nastavení nejsou automaticky generovaný primární klíče, protože jsou obvykle nemá význam pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="8a142-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="8a142-164">Ale v tomto případě má smysl primární klíč a chcete zobrazit.</span><span class="sxs-lookup"><span data-stu-id="8a142-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="8a142-165">Změnit **oddělení** sloupec, který se zobrazí název oddělení.</span><span class="sxs-lookup"><span data-stu-id="8a142-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="8a142-166">Zobrazení kódu `Name` vlastnost entity oddělení, který je načten do `Department` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="8a142-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="8a142-167">Spusťte aplikaci a vyberte **kurzy** kartu pro zobrazení seznamu s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="8a142-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="8a142-169">Vytvoření stránky Instruktoři</span><span class="sxs-lookup"><span data-stu-id="8a142-169">Create an Instructors page</span></span>

<span data-ttu-id="8a142-170">V této části vytvoříte kontroler a zobrazení entity kurzů vedených mohla zobrazit na stránce Instruktoři:</span><span class="sxs-lookup"><span data-stu-id="8a142-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)

<span data-ttu-id="8a142-172">Tato stránka načte a zobrazí související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="8a142-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="8a142-173">Seznam Instruktoři zobrazí související data z OfficeAssignment entity.</span><span class="sxs-lookup"><span data-stu-id="8a142-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="8a142-174">Entity instruktorem a OfficeAssignment jsou ve vztahu k nule nebo jednom.</span><span class="sxs-lookup"><span data-stu-id="8a142-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="8a142-175">Předběžné načítání budete používat pro OfficeAssignment entity.</span><span class="sxs-lookup"><span data-stu-id="8a142-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="8a142-176">Jak jsme vysvětlili výše, předběžné načítání je obvykle mnohem efektivnější, když potřebujete související data pro všechny načtené řádky v tabulce primární.</span><span class="sxs-lookup"><span data-stu-id="8a142-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="8a142-177">V takovém případě budete chtít zobrazit přiřazení office pro všechny zobrazené Instruktoři.</span><span class="sxs-lookup"><span data-stu-id="8a142-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="8a142-178">Když uživatel vybere instruktorem, se zobrazí související entity kurzu.</span><span class="sxs-lookup"><span data-stu-id="8a142-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="8a142-179">Entity instruktorem a kurzu jsou v relaci m: m.</span><span class="sxs-lookup"><span data-stu-id="8a142-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="8a142-180">Předběžné načítání budete používat pro kurz entit a jejich souvisejících entit oddělení.</span><span class="sxs-lookup"><span data-stu-id="8a142-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="8a142-181">Samostatné dotazy v tomto případě může být mnohem efektivnější, protože potřebujete jenom pro vybrané kurzů vedených kurzů.</span><span class="sxs-lookup"><span data-stu-id="8a142-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="8a142-182">Však tento příklad ukazuje způsob použití předběžné načítání pro navigační vlastnosti v rámci entity, které představují samy o sobě v navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8a142-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="8a142-183">Když uživatel vybere kurzu, zobrazí se související data ze sady entit registrace.</span><span class="sxs-lookup"><span data-stu-id="8a142-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="8a142-184">Kurz a registraci entity jsou v vztah jeden mnoho.</span><span class="sxs-lookup"><span data-stu-id="8a142-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="8a142-185">Pro registraci a jejich souvisejících entit Student použijete samostatné dotazy.</span><span class="sxs-lookup"><span data-stu-id="8a142-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="8a142-186">Vytvoření modelu zobrazení pro zobrazení indexu instruktorem</span><span class="sxs-lookup"><span data-stu-id="8a142-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="8a142-187">Na stránce Instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="8a142-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="8a142-188">Proto vytvoříte zobrazení modelu, který obsahuje tři vlastnosti každé obsahující data pro jednu z tabulek.</span><span class="sxs-lookup"><span data-stu-id="8a142-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="8a142-189">V *SchoolViewModels* složku, vytvořte *InstructorIndexData.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8a142-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="8a142-190">Vytvoření kontroleru instruktorem a zobrazení</span><span class="sxs-lookup"><span data-stu-id="8a142-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="8a142-191">Vytvoření řadiče Instruktoři s akcemi čtení/zápisu EF, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="8a142-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Přidat kontroler Instruktoři](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="8a142-193">Otevřít *InstructorsController.cs* a přidejte sadu pomocí příkazu pro modely ViewModels obor názvů:</span><span class="sxs-lookup"><span data-stu-id="8a142-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="8a142-194">Metoda Index nahraďte následující kód, který se nemůžou dočkat, až načítání souvisejících dat a vložit ho do modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8a142-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="8a142-195">Metoda přijímá data volitelné trasy (`id`) a parametru řetězce dotazu (`courseID`), které poskytují hodnoty ID vybrané instruktorem a vybrané kurzu.</span><span class="sxs-lookup"><span data-stu-id="8a142-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="8a142-196">Parametry jsou poskytovány **vyberte** hypertextové odkazy na stránky.</span><span class="sxs-lookup"><span data-stu-id="8a142-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="8a142-197">Vytváření instance zobrazení modelu a jeho uvedením seznamu instruktorů začíná kód.</span><span class="sxs-lookup"><span data-stu-id="8a142-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="8a142-198">Předběžné načítání pro Určuje kód `Instructor.OfficeAssignment` a `Instructor.CourseAssignments` navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8a142-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="8a142-199">V rámci `CourseAssignments` vlastnost, `Course` vlastnosti je načtena a v rámci, která je `Enrollments` a `Department` vlastnosti jsou načtena a v každé `Enrollment` entity `Student` načtena vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8a142-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="8a142-200">Protože zobrazení vždy vyžaduje OfficeAssignment entity, je efektivnější pro načtení, které ve stejném dotazu.</span><span class="sxs-lookup"><span data-stu-id="8a142-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="8a142-201">Kurz entity jsou požadovány při výběru instruktorem na webové stránce, takže pomocí jediného dotazu je lepší než více dotazů jenom v případě, že na stránce se zobrazí častěji kurzu vybrali než bez.</span><span class="sxs-lookup"><span data-stu-id="8a142-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="8a142-202">Kód se opakuje `CourseAssignments` a `Course` vzhledem k tomu, že budete potřebovat dvě vlastnosti z `Course`.</span><span class="sxs-lookup"><span data-stu-id="8a142-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="8a142-203">První řetězec `ThenInclude` volá získá `CourseAssignment.Course`, `Course.Enrollments`, a `Enrollment.Student`.</span><span class="sxs-lookup"><span data-stu-id="8a142-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="8a142-204">V tomto bodě v kódu jiné `ThenInclude` budou platit pro navigační vlastnosti `Student`, které nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="8a142-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="8a142-205">Ale volání `Include` začne přehrávat znovu, s `Instructor` vlastnosti, takže budete muset projít řetězu znovu tento čas zadání `Course.Department` místo `Course.Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="8a142-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="8a142-206">Následující kód provede při instruktorem byla vybrána.</span><span class="sxs-lookup"><span data-stu-id="8a142-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="8a142-207">Vybrané kurzů vedených je načten ze seznamu školitelů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8a142-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="8a142-208">Model zobrazení `Courses` vlastnost je pak načten pomocí kurzu entity z této kurzů vedených `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8a142-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="8a142-209">`Where` Metoda vrátí kolekci, ale v tomto případě kritéria předat výsledek metody v pouze jednu entitu kurzů vedených se vrací.</span><span class="sxs-lookup"><span data-stu-id="8a142-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="8a142-210">`Single` Metoda převede kolekci na jednu entitu instruktorem, která umožňuje přístup k dané entity `CourseAssignments` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8a142-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="8a142-211">`CourseAssignments` Obsahuje vlastnost `CourseAssignment` entity, z nichž chcete pouze související `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="8a142-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="8a142-212">Můžete použít `Single` metody na kolekci, když víte, kolekce budou mít pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="8a142-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="8a142-213">Jedinou metodu vyvolá výjimku, pokud do něho předaný kolekce je prázdná, nebo pokud existuje více než jednu položku.</span><span class="sxs-lookup"><span data-stu-id="8a142-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="8a142-214">Alternativou je `SingleOrDefault`, která vrátí výchozí hodnoty (null v tomto případě) Pokud kolekce je prázdná.</span><span class="sxs-lookup"><span data-stu-id="8a142-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="8a142-215">Ale v tomto případě bude stále výsledkem výjimku (z pokusu o nalezení `Courses` vlastnost na hodnotu Null), a zpráva o výjimce by méně jasně ukazovat na příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="8a142-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="8a142-216">Při volání `Single` metodu, můžete také předat Where podmínku namísto volání metody `Where` metoda samostatně:</span><span class="sxs-lookup"><span data-stu-id="8a142-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="8a142-217">Namísto:</span><span class="sxs-lookup"><span data-stu-id="8a142-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="8a142-218">Dále pokud jste vybrali kurz, vybrané kurzu se načte z seznam kurzů v zobrazení modelu.</span><span class="sxs-lookup"><span data-stu-id="8a142-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="8a142-219">Potom zobrazení modelu `Enrollments` s registrací entity z tohoto kurzu je načtena vlastnost `Enrollments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8a142-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="8a142-220">Úprava zobrazení indexu instruktorem</span><span class="sxs-lookup"><span data-stu-id="8a142-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="8a142-221">V *Views/Instructors/Index.cshtml*, nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="8a142-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="8a142-222">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="8a142-222">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="8a142-223">Stávající kód, které jste udělali následující změny:</span><span class="sxs-lookup"><span data-stu-id="8a142-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="8a142-224">Změnit třídu modelu `InstructorIndexData`.</span><span class="sxs-lookup"><span data-stu-id="8a142-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="8a142-225">Změnil se název stránky z **Index** k **Instruktoři**.</span><span class="sxs-lookup"><span data-stu-id="8a142-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="8a142-226">Přidá **Office** sloupec, který zobrazuje `item.OfficeAssignment.Location` pouze tehdy, pokud `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="8a142-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="8a142-227">(Protože je to vztah jeden: nula nebo 1, nemusí být související entita OfficeAssignment.)</span><span class="sxs-lookup"><span data-stu-id="8a142-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="8a142-228">Přidá **kurzy** sloupec, který zobrazuje kurzy vedené jednotlivých kurzů vedených.</span><span class="sxs-lookup"><span data-stu-id="8a142-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="8a142-229">Zobrazit [explicitní přechod na řádek s `@:` ](xref:mvc/views/razor#explicit-line-transition-with-) Další informace o tuto syntaxi razor.</span><span class="sxs-lookup"><span data-stu-id="8a142-229">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="8a142-230">Přidání kódu, která dynamicky přidá `class="success"` k `tr` element vybrané instruktorem.</span><span class="sxs-lookup"><span data-stu-id="8a142-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="8a142-231">Tím se nastaví barvu pozadí vybraného řádku pomocí Bootstrap třídy.</span><span class="sxs-lookup"><span data-stu-id="8a142-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="8a142-232">Přidat nový popisek hypertextový odkaz **vyberte** bezprostředně před další odkazy na každém řádku, což způsobí, že ID vybraných kurzů vedených k odeslání do `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="8a142-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="8a142-233">Spusťte aplikaci a vyberte **Instruktoři** kartu. Na stránce zobrazí vlastnost umístění související entity OfficeAssignment a na prázdné tabulce buňku, když není žádná související entita OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="8a142-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Instruktoři indexovou stránku, kterou nevybere](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="8a142-235">V *Views/Instructors/Index.cshtml* soubor po zavření tabulky prvku (na konci souboru), přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="8a142-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="8a142-236">Tento kód zobrazí seznam kurzů související s instruktorem, pokud je vybrána instruktorem.</span><span class="sxs-lookup"><span data-stu-id="8a142-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="8a142-237">Tento kód čte `Courses` vlastnost model zobrazení zobrazíte seznam kurzů.</span><span class="sxs-lookup"><span data-stu-id="8a142-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="8a142-238">Poskytuje také **vyberte** hypertextový odkaz, který odesílá ID vybrané kurz `Index` metody akce.</span><span class="sxs-lookup"><span data-stu-id="8a142-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="8a142-239">Aktualizujte stránku a vybrat instruktorem.</span><span class="sxs-lookup"><span data-stu-id="8a142-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="8a142-240">Nyní uvidíte tabulku, která zobrazuje kurzy přiřazen k vybrané instruktorem a jednotlivých kurzů se zobrazí název přiřazený oddělení.</span><span class="sxs-lookup"><span data-stu-id="8a142-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Instruktoři Index stránky kurzů vedených vybrané](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="8a142-242">Za blok kódu, který jste právě přidali přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="8a142-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="8a142-243">Zobrazí seznam studentů, kteří se zaregistrují v kurzu při výběru tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="8a142-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="8a142-244">Tento kód načte vlastnost registrace modelu zobrazení zobrazíte seznam studentů zaregistrovaný do kurzu.</span><span class="sxs-lookup"><span data-stu-id="8a142-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="8a142-245">Znovu aktualizujte stránku a vybrat instruktorem.</span><span class="sxs-lookup"><span data-stu-id="8a142-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="8a142-246">Vyberte kurzu zobrazíte seznam registrovaná studentů a jejich kvality.</span><span class="sxs-lookup"><span data-stu-id="8a142-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Kurzů vedených instruktory Index stránky a vybrali kurzu](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="8a142-248">O explicitní načtení</span><span class="sxs-lookup"><span data-stu-id="8a142-248">About explicit loading</span></span>

<span data-ttu-id="8a142-249">Když jste získali seznam školitelů v *InstructorsController.cs*, jste zadali pro předběžné načítání `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8a142-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="8a142-250">Předpokládejme, že jste očekávali uživatelům jen zřídka byste rádi viděli registrací ve vybrané instruktorem a kurzu.</span><span class="sxs-lookup"><span data-stu-id="8a142-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="8a142-251">V takovém případě můžete chtít načíst data registrací. pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="8a142-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="8a142-252">Chcete-li zobrazit příklad toho, jak provést explicitní načtení, nahraďte `Index` metody následující kód, který odebere předběžné načítání pro registraci a načte vlastnosti explicitně.</span><span class="sxs-lookup"><span data-stu-id="8a142-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="8a142-253">Změny kódu jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="8a142-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="8a142-254">Nový kód klesne *ThenInclude* metoda se volá pro zápis dat z kódu, která načte entity instruktorem.</span><span class="sxs-lookup"><span data-stu-id="8a142-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="8a142-255">Pokud se vybere instruktorem a kurz, zvýrazněný kód načte registrace pro vybraný kurz a Student entit pro každou registrace.</span><span class="sxs-lookup"><span data-stu-id="8a142-255">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="8a142-256">Spustit aplikaci, přejděte na stránku Instruktoři Index a zobrazí obsah zobrazený na stránce, nijak neliší, i když jste změnili, jak načíst data.</span><span class="sxs-lookup"><span data-stu-id="8a142-256">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="8a142-257">Získat kód</span><span class="sxs-lookup"><span data-stu-id="8a142-257">Get the code</span></span>

[<span data-ttu-id="8a142-258">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="8a142-258">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="8a142-259">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8a142-259">Next steps</span></span>

<span data-ttu-id="8a142-260">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="8a142-260">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8a142-261">Zjistili jste, jak načíst související data</span><span class="sxs-lookup"><span data-stu-id="8a142-261">Learned how to load related data</span></span>
> * <span data-ttu-id="8a142-262">Vytvoření stránky kurzy</span><span class="sxs-lookup"><span data-stu-id="8a142-262">Created a Courses page</span></span>
> * <span data-ttu-id="8a142-263">Vytvoří stránku Instruktoři</span><span class="sxs-lookup"><span data-stu-id="8a142-263">Created an Instructors page</span></span>
> * <span data-ttu-id="8a142-264">Dozvěděli jste se o explicitní načtení</span><span class="sxs-lookup"><span data-stu-id="8a142-264">Learned about explicit loading</span></span>

<span data-ttu-id="8a142-265">Přejděte k dalšímu kurzu, přečtěte si, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="8a142-265">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8a142-266">Aktualizace souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="8a142-266">Update related data</span></span>](update-related-data.md)
