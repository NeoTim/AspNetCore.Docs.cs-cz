---
title: 'Kurz: čtení souvisejících dat – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která Entity Framework načíst do vlastností navigace.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: abd5c4e474e30c119e2bea9e3cce3d2b277e0daf
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212742"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="aaaf1-103">Kurz: čtení souvisejících dat – ASP.NET MVC pomocí EF Core</span><span class="sxs-lookup"><span data-stu-id="aaaf1-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="aaaf1-104">V předchozím kurzu jste dokončili model školních dat.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="aaaf1-105">V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která Entity Framework načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="aaaf1-106">Následující ilustrace znázorňují stránky, se kterými budete pracovat.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-106">The following illustrations show the pages that you'll work with.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

<span data-ttu-id="aaaf1-109">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aaaf1-110">Naučte se načítat související data</span><span class="sxs-lookup"><span data-stu-id="aaaf1-110">Learn how to load related data</span></span>
> * <span data-ttu-id="aaaf1-111">Vytvoření stránky kurzů</span><span class="sxs-lookup"><span data-stu-id="aaaf1-111">Create a Courses page</span></span>
> * <span data-ttu-id="aaaf1-112">Vytvoření stránky instruktory</span><span class="sxs-lookup"><span data-stu-id="aaaf1-112">Create an Instructors page</span></span>
> * <span data-ttu-id="aaaf1-113">Informace o explicitním načítání</span><span class="sxs-lookup"><span data-stu-id="aaaf1-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aaaf1-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="aaaf1-114">Prerequisites</span></span>

* [<span data-ttu-id="aaaf1-115">Vytvoření složitého datového modelu</span><span class="sxs-lookup"><span data-stu-id="aaaf1-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="aaaf1-116">Naučte se načítat související data</span><span class="sxs-lookup"><span data-stu-id="aaaf1-116">Learn how to load related data</span></span>

<span data-ttu-id="aaaf1-117">Existuje několik způsobů, jak může software pro mapování relačních dat (ORM), jako je například Entity Framework, načítat související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="aaaf1-118">Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-118">Eager loading.</span></span> <span data-ttu-id="aaaf1-119">Když se entita přečte, načtou se spolu s ní související data.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="aaaf1-120">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="aaaf1-121">Eager načítání můžete zadat v Entity Framework Core pomocí `Include` `ThenInclude` metod a.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="aaaf1-123">Některá data můžete načíst v samostatných dotazech a EF "opravuje" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="aaaf1-124">To znamená, že EF automaticky přidá samostatně načtené entity, kde patří do vlastností navigace dříve načtených entit.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="aaaf1-125">Pro dotaz, který načte související data, můžete použít `Load` metodu namísto metody, která vrací seznam nebo objekt, například `ToList` nebo `Single` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="aaaf1-127">Explicitní načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-127">Explicit loading.</span></span> <span data-ttu-id="aaaf1-128">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="aaaf1-129">Napíšete kód, který načte související data, pokud je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="aaaf1-130">Stejně jako v případě, že se Eager načítá pomocí samostatných dotazů, explicitní načítání vede k více dotazům odesílaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="aaaf1-131">Rozdíl je v tom, že při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="aaaf1-132">V Entity Framework Core 1,1 lze použít `Load` metodu k explicitnímu načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="aaaf1-133">Zde je příklad:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-133">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="aaaf1-135">Opožděné načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-135">Lazy loading.</span></span> <span data-ttu-id="aaaf1-136">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="aaaf1-137">Při prvním pokusu o přístup k navigační vlastnosti je však automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="aaaf1-138">Dotaz se pošle do databáze pokaždé, když se pokusíte získat data z navigační vlastnosti poprvé.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="aaaf1-139">Entity Framework Core 1,0 nepodporuje opožděné načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="aaaf1-140">Otázky výkonu</span><span class="sxs-lookup"><span data-stu-id="aaaf1-140">Performance considerations</span></span>

<span data-ttu-id="aaaf1-141">Pokud víte, že pro každou načtenou entitu potřebujete související data, Eager načítání často nabízí nejlepší výkon, protože jediný dotaz odeslaný do databáze je obvykle efektivnější než samostatné dotazy pro každou načtenou entitu.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="aaaf1-142">Předpokládejme například, že každé oddělení má deset souvisejících kurzů.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="aaaf1-143">Eager načtení všech souvisejících dat by způsobilo pouze jeden (JOIN) dotaz a jednu zpáteční cestu do databáze.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="aaaf1-144">Samostatný dotaz na kurzy pro každé oddělení by způsobil jedenácté cestování databáze.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="aaaf1-145">Další výměna cest k databázi je obzvláště neškodná na výkon, pokud je latence vysoká.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="aaaf1-146">Naopak v některých scénářích jsou samostatné dotazy efektivnější.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="aaaf1-147">Eager načtení všech souvisejících dat v jednom dotazu může způsobit vygenerování velmi složitého připojení, které SQL Server nemůže efektivně zpracovat.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="aaaf1-148">Nebo pokud potřebujete přístup k vlastnostem navigace entity jenom pro podmnožinu sady entit, které zpracováváte, můžou se samostatné dotazy provádět lépe, protože Eager načítání všeho dopředu by načetlo víc dat, než kolik potřebujete.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="aaaf1-149">Pokud je výkon kritický, je nejlepší testovat výkon oběma způsoby, abyste mohli nejlépe vybrat.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="aaaf1-150">Vytvoření stránky kurzů</span><span class="sxs-lookup"><span data-stu-id="aaaf1-150">Create a Courses page</span></span>

<span data-ttu-id="aaaf1-151">Entita kurzu obsahuje navigační vlastnost, která obsahuje entitu oddělení oddělení, ke které je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="aaaf1-152">Pokud chcete v seznamu kurzů zobrazit název přiřazeného oddělení, musíte získat vlastnost Name z entity oddělení, která je ve `Course.Department` vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="aaaf1-153">Vytvořte řadič s názvem CoursesController pro typ entity kurzu pomocí stejných možností pro **kontroler MVC se zobrazeními, a to pomocí entity Frameworkého** uživatelského rozhraní, které jste předtím vytvořili pro řadič studenta, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Přidat kontroler kurzů](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="aaaf1-155">Otevřete *CoursesController.cs* a prověřte `Index` metodu.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="aaaf1-156">Automatické generování uživatelského rozhraní pro vlastnost navigace určilo načítání Eager `Department` pomocí `Include` metody.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="aaaf1-157">Nahraďte `Index` metodu následujícím kódem, který používá vhodnější název pro `IQueryable` , který vrací entity kurzu ( `courses` místo `schoolContext` ):</span><span class="sxs-lookup"><span data-stu-id="aaaf1-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="aaaf1-158">Otevřete *zobrazení/kurzy/index. cshtml* a nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="aaaf1-159">Změny jsou zvýrazněny:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="aaaf1-160">Provedli jste následující změny ve vygenerovaném kódu:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="aaaf1-161">Změnili jste záhlaví z indexu na kurzy.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="aaaf1-162">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="aaaf1-163">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="aaaf1-164">V tomto případě je však primární klíč smysluplný a chcete jej zobrazit.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="aaaf1-165">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="aaaf1-166">Kód zobrazí `Name` vlastnost entity oddělení, která je načtena do `Department` navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="aaaf1-167">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="aaaf1-169">Vytvoření stránky instruktory</span><span class="sxs-lookup"><span data-stu-id="aaaf1-169">Create an Instructors page</span></span>

<span data-ttu-id="aaaf1-170">V této části vytvoříte kontrolér a zobrazení pro entitu instruktora, aby se zobrazila stránka instruktoři:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

<span data-ttu-id="aaaf1-172">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="aaaf1-173">Seznam instruktorů zobrazuje související data z entity OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="aaaf1-174">Entity instruktora a OfficeAssignment jsou v relaci typu 1:1 nebo 1:1.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="aaaf1-175">Pro entity OfficeAssignment budete používat Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="aaaf1-176">Jak bylo vysvětleno dříve, načítání Eager je obvykle efektivnější, pokud potřebujete související data pro všechny načtené řádky primární tabulky.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="aaaf1-177">V takovém případě chcete zobrazit přiřazení Office pro všechny zobrazené instruktory.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="aaaf1-178">Když uživatel vybere instruktora, zobrazí se související entity kurzu.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="aaaf1-179">Entity instruktor a Course jsou v relaci n:n.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="aaaf1-180">Pro entity kurzu a jejich související entity oddělení budete používat Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="aaaf1-181">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete kurzy jenom pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="aaaf1-182">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v rámci entit, které jsou samy v navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="aaaf1-183">Když uživatel vybere kurz, zobrazí se související data ze sady entit registrace.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="aaaf1-184">Entity kurzu a registrace jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="aaaf1-185">Pro entity registrace a jejich související entity studenta použijete samostatné dotazy.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="aaaf1-186">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="aaaf1-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="aaaf1-187">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="aaaf1-188">Proto vytvoříte model zobrazení, který obsahuje tři vlastnosti, z nichž každý uchovává data pro jednu z tabulek.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="aaaf1-189">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="aaaf1-190">Vytvoření kontroleru a zobrazení instruktora</span><span class="sxs-lookup"><span data-stu-id="aaaf1-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="aaaf1-191">Vytvořte kontrolora instruktorů s akcemi čtení/zápisu EF, jak je znázorněno na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Přidat kontroler instruktorů](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="aaaf1-193">Otevřete *InstructorsController.cs* a přidejte příkaz using pro obor názvů ViewModels:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="aaaf1-194">Nahraďte metodu indexu následujícím kódem pro Eager načítání souvisejících dat a umístěte je do modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="aaaf1-195">Metoda přijímá volitelná data směrování ( `id` ) a parametr řetězce dotazu ( `courseID` ), které poskytují hodnoty ID vybraného instruktora a vybraného kurzu.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="aaaf1-196">Parametry jsou k dispozici na stránce **vybrané** hypertextové odkazy.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="aaaf1-197">Kód začíná vytvořením instance modelu zobrazení a jeho vložením do seznamu instruktorů.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="aaaf1-198">Kód určuje načítání Eager pro `Instructor.OfficeAssignment` a `Instructor.CourseAssignments` vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="aaaf1-199">V rámci `CourseAssignments` vlastnosti `Course` je načtena vlastnost a v rámci ní `Enrollments` `Department` jsou načteny vlastnosti a a v rámci každé `Enrollment` entity `Student` je načtena vlastnost.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="aaaf1-200">Vzhledem k tomu, že zobrazení vždy vyžaduje entitu OfficeAssignment, je efektivnější ho načíst ve stejném dotazu.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="aaaf1-201">Entity kurzu jsou požadovány, když je na webové stránce vybrán instruktor. jediný dotaz je lepší než více dotazů pouze v případě, že je stránka zobrazena častěji s kurzem vybraným než bez.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="aaaf1-202">Kód se opakuje `CourseAssignments` a `Course` protože potřebujete dvě vlastnosti z `Course` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="aaaf1-203">První řetězec `ThenInclude` volání získá `CourseAssignment.Course` , `Course.Enrollments` a `Enrollment.Student` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="aaaf1-204">V tomto okamžiku v kódu, další `ThenInclude` by byl pro navigační vlastnosti `Student` , které nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="aaaf1-205">Ale volání `Include` začíná s `Instructor` vlastnostmi, takže musíte projít řetěz znovu, tentokrát `Course.Department` místo `Course.Enrollments` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="aaaf1-206">Následující kód se spustí, když byl vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="aaaf1-207">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="aaaf1-208">Vlastnost modelu zobrazení `Courses` je pak načtena s entitami kurzu z vlastnosti navigace tohoto vyučujícího `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="aaaf1-209">`Where`Metoda vrací kolekci, ale v tomto případě kritéria předaná této metodě mají za následek vrácenou pouze jednu entitu Instructor.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="aaaf1-210">`Single`Metoda převede kolekci na jednu entitu instruktora, která vám umožní přístup k vlastnosti této entity `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="aaaf1-211">`CourseAssignments`Vlastnost obsahuje `CourseAssignment` entity, ze kterých chcete pouze související `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="aaaf1-212">`Single`Metodu pro kolekci použijete, když víte, že kolekce bude obsahovat pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="aaaf1-213">Jediná metoda vyvolá výjimku, pokud je kolekce předána prázdná, nebo pokud existuje více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="aaaf1-214">Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="aaaf1-215">V tomto případě by však došlo k výjimce (při pokusu o nalezení `Courses` vlastnosti v odkazu s hodnotou null) a zpráva o výjimce by byla méně zřetelně indikovat příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="aaaf1-216">Při volání `Single` metody lze také předat podmínku WHERE namísto volání `Where` metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="aaaf1-217">Namísto:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="aaaf1-218">V dalším případě se vybraný kurz načte ze seznamu kurzů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="aaaf1-219">Pak je vlastnost zobrazení modelu `Enrollments` načtena s entitami registrace z vlastnosti navigace tohoto kurzu `Enrollments` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="aaaf1-220">Úprava zobrazení indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="aaaf1-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="aaaf1-221">V *zobrazeních, instruktorech/index. cshtml*nahraďte kód šablony následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="aaaf1-222">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-222">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="aaaf1-223">V existujícím kódu jste provedli následující změny:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="aaaf1-224">Třída modelu se změnila na `InstructorIndexData` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="aaaf1-225">Změnila se název stránky z **indexu** na **instruktory**.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="aaaf1-226">Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v případě, že `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="aaaf1-227">(Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.)</span><span class="sxs-lookup"><span data-stu-id="aaaf1-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="aaaf1-228">Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="aaaf1-229">Další informace najdete v části [explicitní přechod na řádku](xref:mvc/views/razor#explicit-line-transition) v Razor článku syntaxe.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="aaaf1-230">Přidaný kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="aaaf1-231">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="aaaf1-232">Přidali jsme nový hypertextový odkaz **označený těsně před** ostatní odkazy v každém řádku, což způsobí odeslání vybraného ID instruktoru do `Index` metody.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="aaaf1-233">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje vlastnost umístění souvisejících entit OfficeAssignment a prázdnou buňku tabulky, pokud neexistuje žádná související entita OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Žádná vybraná stránka indexu instruktorů](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="aaaf1-235">V souboru *views/instruktors/index. cshtml* za uzavírací element Table (na konci souboru) přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="aaaf1-236">Tento kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="aaaf1-237">Tento kód načte `Courses` vlastnost modelu zobrazení a zobrazí seznam kurzů.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="aaaf1-238">Poskytuje také hypertextový odkaz pro **Výběr** , který pošle ID vybraného kurzu do `Index` metody Action.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="aaaf1-239">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="aaaf1-240">Nyní se zobrazí mřížka zobrazující kurzy přiřazené k vybranému instruktorovi a pro každý kurz vidíte název přiřazeného oddělení.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Vybraná instruktora stránky indexu instruktorů](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="aaaf1-242">Po tom, co jste právě přidali blok kódu, přidejte následující kód.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="aaaf1-243">Zobrazí se seznam studentů, kteří jsou v kurzu při výběru tohoto kurzu zaregistrovaní.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="aaaf1-244">Tento kód načte vlastnost registrace modelu zobrazení, aby se zobrazil seznam studentů zaregistrovaných v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="aaaf1-245">Aktualizujte stránku znovu a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="aaaf1-246">Pak vyberte kurz, ve kterém se zobrazí seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="aaaf1-248">O explicitním načítání</span><span class="sxs-lookup"><span data-stu-id="aaaf1-248">About explicit loading</span></span>

<span data-ttu-id="aaaf1-249">Když jste načetli seznam instruktorů v *InstructorsController.cs*, zadali jste Eager načítání pro `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="aaaf1-250">Předpokládejme, že jste uživatelům očekávali jenom zřídka jenom ty registrace ve vybraném instruktoru a kurzu.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="aaaf1-251">V takovém případě může být vhodné načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="aaaf1-252">Chcete-li zobrazit příklad explicitního načítání, nahraďte `Index` metodu následujícím kódem, který odebere Eager načítání pro zápisy a explicitně načte tuto vlastnost.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="aaaf1-253">Změny kódu jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="aaaf1-254">Nový kód zruší volání metody *ThenInclude* pro data zápisu z kódu, který načítá entity instruktora.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="aaaf1-255">Tím také klesne `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="aaaf1-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="aaaf1-256">Pokud je vybrán instruktor a kurz, zvýrazněný kód načte entity registrace pro vybraný kurz a entity studenta pro každou registraci.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="aaaf1-257">Spusťte aplikaci, přejděte na stránku indexu instruktory nyní a uvidíte, že se na stránce zobrazí žádný rozdíl, i když jste změnili způsob, jakým se data načítají.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="aaaf1-258">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="aaaf1-258">Get the code</span></span>

[<span data-ttu-id="aaaf1-259">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="aaaf1-260">Další kroky</span><span class="sxs-lookup"><span data-stu-id="aaaf1-260">Next steps</span></span>

<span data-ttu-id="aaaf1-261">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="aaaf1-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aaaf1-262">Zjistili jste, jak načíst související data</span><span class="sxs-lookup"><span data-stu-id="aaaf1-262">Learned how to load related data</span></span>
> * <span data-ttu-id="aaaf1-263">Stránka vytvoření kurzů</span><span class="sxs-lookup"><span data-stu-id="aaaf1-263">Created a Courses page</span></span>
> * <span data-ttu-id="aaaf1-264">Stránka s instruktory se vytvořila.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-264">Created an Instructors page</span></span>
> * <span data-ttu-id="aaaf1-265">Dozvěděli jste se o explicitním načítání.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-265">Learned about explicit loading</span></span>

<span data-ttu-id="aaaf1-266">Přejděte k dalšímu kurzu, kde se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="aaaf1-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="aaaf1-267">Aktualizace souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="aaaf1-267">Update related data</span></span>](update-related-data.md)
