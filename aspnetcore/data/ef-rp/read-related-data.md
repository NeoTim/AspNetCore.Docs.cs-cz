---
title: Razor Pages s EF Core ve ASP.NET Core – data související s čtením – 6 z 8
author: rick-anderson
description: V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která Entity Framework načíst do vlastností navigace.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: d244ce1527486466bcbc6557ec35869aa206bc4f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656574"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="b7655-103">Razor Pages s EF Core ve ASP.NET Core – data související s čtením – 6 z 8</span><span class="sxs-lookup"><span data-stu-id="b7655-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="b7655-104">Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7655-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7655-105">V tomto kurzu se dozvíte, jak číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="b7655-106">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="b7655-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b7655-107">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="b7655-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="b7655-110">Eager, explicitní a opožděné načítání</span><span class="sxs-lookup"><span data-stu-id="b7655-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="b7655-111">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="b7655-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b7655-112">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b7655-113">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b7655-114">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b7655-115">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="b7655-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b7655-116">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="b7655-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b7655-117">Vystavení více dotazů může být efektivnější než obří jediný dotaz.</span><span class="sxs-lookup"><span data-stu-id="b7655-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="b7655-118">Eager načítání je zadáno pomocí metod `Include` a `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="b7655-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b7655-120">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="b7655-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b7655-121">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="b7655-121">One query for the main query</span></span> 
  * <span data-ttu-id="b7655-122">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="b7655-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b7655-123">Oddělte dotazy pomocí `Load`: data lze načíst v samostatných dotazech a EF Core "" opravuje "navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7655-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b7655-124">"Opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7655-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b7655-125">Samostatné dotazy s `Load` jsou lépe podobné jako explicitní načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b7655-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b7655-127">Poznámka: EF Core automaticky opravuje navigační vlastnosti pro všechny další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="b7655-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b7655-128">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="b7655-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b7655-129">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b7655-130">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b7655-131">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="b7655-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b7655-132">Explicitní načítání pomocí samostatných dotazů má za následek odeslání více dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="b7655-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="b7655-133">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="b7655-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b7655-134">K provedení explicitního načítání použijte metodu `Load`.</span><span class="sxs-lookup"><span data-stu-id="b7655-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b7655-135">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b7655-135">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b7655-137">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b7655-138">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b7655-139">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b7655-140">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="b7655-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b7655-141">Dotaz je odeslán do databáze při každém prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7655-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="b7655-142">Vytvořit stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="b7655-142">Create Course pages</span></span>

<span data-ttu-id="b7655-143">Entita `Course` obsahuje navigační vlastnost, která obsahuje související entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<span data-ttu-id="b7655-145">Chcete-li zobrazit název přiřazeného oddělení za kurz:</span><span class="sxs-lookup"><span data-stu-id="b7655-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="b7655-146">Načtěte související `Department` entitu do vlastnosti navigace `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="b7655-147">Získá název z vlastnosti `Name` `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="b7655-148">Stránky kurzu generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="b7655-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7655-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7655-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7655-150">Postupujte podle pokynů v části [stránky pro studenty](xref:data/ef-rp/intro#scaffold-student-pages) s těmito výjimkami:</span><span class="sxs-lookup"><span data-stu-id="b7655-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b7655-151">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="b7655-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="b7655-152">Pro třídu modelu použijte `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="b7655-153">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="b7655-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7655-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7655-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b7655-155">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="b7655-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="b7655-156">Spusťte následující příkaz pro generování uživatelského rozhraní stránek kurzu.</span><span class="sxs-lookup"><span data-stu-id="b7655-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="b7655-157">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="b7655-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="b7655-158">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="b7655-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="b7655-159">Otevřete *stránky/kurzy/index. cshtml. cs* a Projděte si metodu `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="b7655-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b7655-160">Modul generování uživatelského rozhraní zadal Eager načítání pro vlastnost navigace `Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b7655-161">Metoda `Include` určuje načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="b7655-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="b7655-162">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="b7655-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b7655-163">Ve sloupci oddělení se zobrazí `DepartmentID`, což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="b7655-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="b7655-164">Zobrazit název oddělení</span><span class="sxs-lookup"><span data-stu-id="b7655-164">Display the department name</span></span>

<span data-ttu-id="b7655-165">Aktualizujte stránky/kurzy/index. cshtml. cs s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7655-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="b7655-166">Předchozí kód změní vlastnost `Course` na `Courses` a přidá `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="b7655-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="b7655-167">`AsNoTracking` zvyšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="b7655-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b7655-168">Entity není nutné sledovat, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="b7655-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b7655-169">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="b7655-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="b7655-170">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="b7655-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b7655-171">Změnili `Course` název vlastnosti na hodnotu `Courses`.</span><span class="sxs-lookup"><span data-stu-id="b7655-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="b7655-172">Byl přidán sloupec **číslo** , který zobrazuje hodnotu vlastnosti `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="b7655-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b7655-173">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="b7655-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b7655-174">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="b7655-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b7655-175">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="b7655-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b7655-176">Kód zobrazí vlastnost `Name` `Department` entity, která je načtena do navigační vlastnosti `Department`:</span><span class="sxs-lookup"><span data-stu-id="b7655-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b7655-177">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="b7655-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b7655-179">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="b7655-179">Loading related data with Select</span></span>

<span data-ttu-id="b7655-180">Metoda `OnGetAsync` načítá související data pomocí metody `Include`.</span><span class="sxs-lookup"><span data-stu-id="b7655-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="b7655-181">Metoda `Select` je alternativou, která načte pouze související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="b7655-182">Pro jednotlivé položky, jako je `Department.Name` používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="b7655-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b7655-183">U kolekcí používá jiný přístup k databázi, ale proto operátor `Include` v kolekcích.</span><span class="sxs-lookup"><span data-stu-id="b7655-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b7655-184">Následující kód načte související data pomocí metody `Select`:</span><span class="sxs-lookup"><span data-stu-id="b7655-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="b7655-185">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b7655-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b7655-186">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="b7655-186">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="b7655-187">Vytvořit stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="b7655-187">Create Instructor pages</span></span>

<span data-ttu-id="b7655-188">Tato část vygeneruje stránky instruktora a přidá související kurzy a registrace na stránku indexu instruktorů.</span><span class="sxs-lookup"><span data-stu-id="b7655-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<span data-ttu-id="b7655-189"><a name="IP"></a>stránka indexu instruktorů ![
](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="b7655-189"><a name="IP"></a>
![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="b7655-190">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="b7655-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b7655-191">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entitu (kancelář na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="b7655-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b7655-192">Entity `Instructor` a `OfficeAssignment` jsou v relaci jednosměrového a nulového vztahu.</span><span class="sxs-lookup"><span data-stu-id="b7655-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b7655-193">Pro entity `OfficeAssignment` se používá Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b7655-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b7655-194">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b7655-195">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="b7655-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b7655-196">Když uživatel vybere instruktora, zobrazí se související entity `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="b7655-197">Entity `Instructor` a `Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="b7655-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b7655-198">Eager načítání se používá pro entity `Course` a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b7655-199">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b7655-200">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="b7655-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b7655-201">Když uživatel vybere kurz, zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b7655-202">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="b7655-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b7655-203">Entity `Course` a `Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="b7655-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="b7655-204">Vytvoření modelu zobrazení</span><span class="sxs-lookup"><span data-stu-id="b7655-204">Create a view model</span></span>

<span data-ttu-id="b7655-205">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="b7655-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b7655-206">Model zobrazení je potřeba, který obsahuje tři vlastnosti reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="b7655-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="b7655-207">Vytvořte *SchoolViewModels/InstructorIndexData. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7655-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="b7655-208">Stránky instruktora uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="b7655-208">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7655-209">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7655-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7655-210">Postupujte podle pokynů v [části generování uživatelského rozhraní se stránkami studenta](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:</span><span class="sxs-lookup"><span data-stu-id="b7655-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b7655-211">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="b7655-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="b7655-212">Pro třídu modelu použijte `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b7655-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="b7655-213">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="b7655-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7655-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7655-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b7655-215">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="b7655-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="b7655-216">Spusťte následující příkaz pro generování uživatelského rozhraní stránek instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="b7655-217">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="b7655-217">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="b7655-218">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="b7655-218">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b7655-219">Chcete-li zjistit, jaké vygenerované stránky vypadá před tím, než je aktualizujete, spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b7655-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="b7655-220">Aktualizovat *stránky/instruktory/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7655-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="b7655-221">Metoda `OnGetAsync` akceptuje volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b7655-222">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="b7655-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="b7655-223">Kód určuje načítání Eager pro následující navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b7655-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="b7655-224">Všimněte si opakování `Include` a `ThenInclude` metod pro `CourseAssignments` a `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="b7655-225">Toto opakování je nezbytné pro zadání Eager načítání pro dvě navigační vlastnosti entity `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="b7655-226">Následující kód se spustí, když je vybrán instruktor (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="b7655-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="b7655-227">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b7655-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b7655-228">Vlastnost `Courses` modelu zobrazení je načtena s `Course` entit z `CourseAssignments` navigační vlastnost tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="b7655-229">Metoda `Where` vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="b7655-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="b7655-230">Ale v tomto případě filtr vybere jednu entitu.</span><span class="sxs-lookup"><span data-stu-id="b7655-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="b7655-231">Proto je volána metoda `Single` pro převod kolekce na jedinou entitu `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b7655-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b7655-232">Entita `Instructor` poskytuje přístup k vlastnosti `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="b7655-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b7655-233">`CourseAssignments` poskytuje přístup k souvisejícím entitám `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b7655-235">Metoda `Single` se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="b7655-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b7655-236">Metoda `Single` vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="b7655-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b7655-237">Alternativa je `SingleOrDefault`, která vrací výchozí hodnotu (v tomto případě hodnotu null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="b7655-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="b7655-238">Následující kód naplní vlastnost `Enrollments` modelu zobrazení, když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="b7655-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b7655-239">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="b7655-239">Update the instructors Index page</span></span>

<span data-ttu-id="b7655-240">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="b7655-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="b7655-241">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="b7655-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b7655-242">Aktualizuje direktivu `page` z `@page` na `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="b7655-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b7655-243">`"{id:int?}"` je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="b7655-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b7655-244">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="b7655-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b7655-245">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze direktivu `@page`, se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="b7655-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="b7655-246">Když je direktiva stránky `@page "{id:int?}"`, adresa URL:</span><span class="sxs-lookup"><span data-stu-id="b7655-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="b7655-247">Přidá sloupec **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v případě, že `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="b7655-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b7655-248">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b7655-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b7655-249">Přidá sloupec **kurzů** , ve kterém se zobrazují kurzy, které každý instruktor prodlužuje.</span><span class="sxs-lookup"><span data-stu-id="b7655-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b7655-250">Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="b7655-250">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="b7655-251">Přidá kód, který dynamicky přidává `class="success"` do `tr`ho prvku vybraného instruktora a kurzu.</span><span class="sxs-lookup"><span data-stu-id="b7655-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="b7655-252">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="b7655-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b7655-253">Přidá nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="b7655-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b7655-254">Tento odkaz odešle ID vybraného instruktoru do metody `Index` a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="b7655-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="b7655-255">Přidá tabulku kurzů pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="b7655-256">Přidá tabulku zápisů studentů pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b7655-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="b7655-257">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b7655-258">Pokud je `OfficeAssignment` null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="b7655-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b7655-259">Klikněte na odkaz **Vybrat** pro instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="b7655-260">Zobrazí se změna stylu řádku a kurzy přiřazené k tomuto instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="b7655-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="b7655-261">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="b7655-261">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="b7655-263">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="b7655-263">Using Single</span></span>

<span data-ttu-id="b7655-264">Metoda `Single` může předat podmínku `Where` namísto volání metody `Where` samostatně:</span><span class="sxs-lookup"><span data-stu-id="b7655-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b7655-265">Použití `Single` s podmínkou WHERE je záležitostí osobní preference.</span><span class="sxs-lookup"><span data-stu-id="b7655-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="b7655-266">Neposkytuje žádné výhody při použití metody `Where`.</span><span class="sxs-lookup"><span data-stu-id="b7655-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b7655-267">explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="b7655-267">Explicit loading</span></span>

<span data-ttu-id="b7655-268">Aktuální kód určuje Eager načítání pro `Enrollments` a `Students`:</span><span class="sxs-lookup"><span data-stu-id="b7655-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="b7655-269">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="b7655-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b7655-270">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="b7655-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b7655-271">V této části je `OnGetAsync` aktualizován tak, aby používal explicitní načítání `Enrollments` a `Students`.</span><span class="sxs-lookup"><span data-stu-id="b7655-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b7655-272">Aktualizujte *stránky/instruktory/index. cshtml. cs* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="b7655-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="b7655-273">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="b7655-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b7655-274">Pokud je vybrán kurz, explicitní načítání kódu načte:</span><span class="sxs-lookup"><span data-stu-id="b7655-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="b7655-275">Entity `Enrollment` pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b7655-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b7655-276">`Student` entit pro každý `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b7655-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b7655-277">Všimněte si, že předchozí komentáře kódu `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="b7655-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b7655-278">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b7655-279">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7655-279">Test the app.</span></span> <span data-ttu-id="b7655-280">V perspektivě uživatele se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="b7655-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b7655-281">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b7655-281">Next steps</span></span>

<span data-ttu-id="b7655-282">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="b7655-283">[Předchozí kurz](xref:data/ef-rp/complex-data-model)
>[Další kurz](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b7655-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b7655-284">V tomto kurzu se budou číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="b7655-285">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="b7655-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b7655-286">Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="b7655-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="b7655-287">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b7655-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b7655-288">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="b7655-288">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="b7655-291">Eager, explicitní a opožděné načítání souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="b7655-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="b7655-292">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="b7655-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b7655-293">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b7655-294">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b7655-295">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b7655-296">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="b7655-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b7655-297">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="b7655-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b7655-298">Vystavení více dotazů může být efektivnější než u některých dotazů v EF6, kde existoval jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="b7655-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="b7655-299">Eager načítání je zadáno pomocí metod `Include` a `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="b7655-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b7655-301">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="b7655-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b7655-302">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="b7655-302">One query for the main query</span></span> 
  * <span data-ttu-id="b7655-303">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="b7655-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b7655-304">Oddělte dotazy pomocí `Load`: data lze načíst v samostatných dotazech a EF Core "" opravuje "navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7655-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b7655-305">"opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7655-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b7655-306">Samostatné dotazy s `Load` jsou lépe podobné jako explicitní načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b7655-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b7655-308">Poznámka: EF Core automaticky opravuje navigační vlastnosti pro všechny další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="b7655-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b7655-309">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="b7655-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b7655-310">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b7655-311">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b7655-312">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="b7655-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b7655-313">Explicitní načítání pomocí samostatných dotazů má za následek více dotazů odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="b7655-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="b7655-314">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="b7655-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b7655-315">K provedení explicitního načítání použijte metodu `Load`.</span><span class="sxs-lookup"><span data-stu-id="b7655-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b7655-316">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b7655-316">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b7655-318">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b7655-319">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b7655-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b7655-320">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b7655-321">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="b7655-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b7655-322">Dotaz se pošle do databáze při prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7655-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="b7655-323">Operátor `Select` načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="b7655-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="b7655-324">Vytvoří stránku kurzu, která zobrazuje název oddělení.</span><span class="sxs-lookup"><span data-stu-id="b7655-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="b7655-325">Entita kurzu obsahuje navigační vlastnost, která obsahuje entitu `Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="b7655-326">Entita `Department` obsahuje oddělení, ke kterému je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="b7655-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="b7655-327">Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů:</span><span class="sxs-lookup"><span data-stu-id="b7655-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="b7655-328">Získá vlastnost `Name` z entity `Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="b7655-329">Entita `Department` přichází z navigační vlastnosti `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="b7655-331">Generování uživatelského rozhraní modelu kurzu</span><span class="sxs-lookup"><span data-stu-id="b7655-331">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7655-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7655-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b7655-333">Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b7655-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7655-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7655-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b7655-335">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b7655-335">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="b7655-336">Předchozí příkaz vygeneruje model `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="b7655-337">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b7655-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="b7655-338">Otevřete *stránky/kurzy/index. cshtml. cs* a Projděte si metodu `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="b7655-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b7655-339">Modul generování uživatelského rozhraní zadal Eager načítání pro vlastnost navigace `Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b7655-340">Metoda `Include` určuje načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="b7655-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="b7655-341">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="b7655-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b7655-342">Ve sloupci oddělení se zobrazí `DepartmentID`, což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="b7655-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="b7655-343">Aktualizujte `OnGetAsync` metodu pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b7655-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="b7655-344">Předchozí kód přidá `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="b7655-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="b7655-345">`AsNoTracking` zvyšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="b7655-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b7655-346">Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="b7655-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b7655-347">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="b7655-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="b7655-348">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="b7655-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b7655-349">Změnili jste záhlaví z indexu na kurzy.</span><span class="sxs-lookup"><span data-stu-id="b7655-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="b7655-350">Byl přidán sloupec **číslo** , který zobrazuje hodnotu vlastnosti `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="b7655-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b7655-351">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="b7655-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b7655-352">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="b7655-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b7655-353">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="b7655-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b7655-354">Kód zobrazí vlastnost `Name` `Department` entity, která je načtena do navigační vlastnosti `Department`:</span><span class="sxs-lookup"><span data-stu-id="b7655-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b7655-355">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="b7655-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b7655-357">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="b7655-357">Loading related data with Select</span></span>

<span data-ttu-id="b7655-358">Metoda `OnGetAsync` načítá související data pomocí metody `Include`:</span><span class="sxs-lookup"><span data-stu-id="b7655-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b7655-359">Operátor `Select` načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="b7655-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="b7655-360">Pro jednotlivé položky, jako je `Department.Name` používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="b7655-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b7655-361">U kolekcí používá jiný přístup k databázi, ale proto operátor `Include` v kolekcích.</span><span class="sxs-lookup"><span data-stu-id="b7655-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b7655-362">Následující kód načte související data pomocí metody `Select`:</span><span class="sxs-lookup"><span data-stu-id="b7655-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b7655-363">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b7655-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b7655-364">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="b7655-364">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="b7655-365">Vytvoření stránky instruktory, která zobrazuje kurzy a registrace</span><span class="sxs-lookup"><span data-stu-id="b7655-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="b7655-366">V této části se vytvoří stránka instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b7655-366">In this section, the Instructors page is created.</span></span>

<span data-ttu-id="b7655-367"><a name="IP"></a>stránka indexu instruktorů ![
](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="b7655-367"><a name="IP"></a>
![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="b7655-368">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="b7655-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b7655-369">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entitu (kancelář na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="b7655-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b7655-370">Entity `Instructor` a `OfficeAssignment` jsou v relaci jednosměrového a nulového vztahu.</span><span class="sxs-lookup"><span data-stu-id="b7655-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b7655-371">Pro entity `OfficeAssignment` se používá Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b7655-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b7655-372">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b7655-373">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="b7655-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b7655-374">Když uživatel vybere instruktora (Harui na předchozím obrázku), zobrazí se související entity `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="b7655-375">Entity `Instructor` a `Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="b7655-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b7655-376">Eager načítání se používá pro entity `Course` a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b7655-377">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b7655-378">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="b7655-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b7655-379">Když uživatel vybere kurz (chemie na předchozím obrázku), zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b7655-380">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="b7655-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b7655-381">Entity `Course` a `Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="b7655-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="b7655-382">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="b7655-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="b7655-383">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="b7655-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b7655-384">Vytvoří se model zobrazení, který obsahuje tři entity reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="b7655-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="b7655-385">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7655-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="b7655-386">Generování uživatelského rozhraní modelu instruktor</span><span class="sxs-lookup"><span data-stu-id="b7655-386">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7655-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7655-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b7655-388">Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b7655-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b7655-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b7655-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b7655-390">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b7655-390">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b7655-391">Předchozí příkaz vygeneruje model `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b7655-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="b7655-392">Spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b7655-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="b7655-393">*Stránky/instruktořis/index. cshtml. cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7655-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="b7655-394">Metoda `OnGetAsync` akceptuje volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b7655-395">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="b7655-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b7655-396">Dotaz obsahuje dvě:</span><span class="sxs-lookup"><span data-stu-id="b7655-396">The query has two includes:</span></span>

* <span data-ttu-id="b7655-397">`OfficeAssignment`: zobrazí se v [zobrazení instruktory](#IP).</span><span class="sxs-lookup"><span data-stu-id="b7655-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="b7655-398">`CourseAssignments`: který přináší výukové kurzy.</span><span class="sxs-lookup"><span data-stu-id="b7655-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b7655-399">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="b7655-399">Update the instructors Index page</span></span>

<span data-ttu-id="b7655-400">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b7655-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="b7655-401">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="b7655-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="b7655-402">Aktualizuje direktivu `page` z `@page` na `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="b7655-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b7655-403">`"{id:int?}"` je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="b7655-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b7655-404">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="b7655-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b7655-405">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze direktivu `@page`, se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="b7655-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="b7655-406">Při `@page "{id:int?}"`direktivy stránky je předchozí adresa URL:</span><span class="sxs-lookup"><span data-stu-id="b7655-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="b7655-407">Nadpis stránky jsou **instruktory**.</span><span class="sxs-lookup"><span data-stu-id="b7655-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="b7655-408">Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v případě, že `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="b7655-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b7655-409">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b7655-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b7655-410">Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem.</span><span class="sxs-lookup"><span data-stu-id="b7655-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b7655-411">Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="b7655-411">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="b7655-412">Přidaný kód, který dynamicky přidává `class="success"` do `tr`ho prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="b7655-413">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="b7655-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b7655-414">Byl přidán nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="b7655-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b7655-415">Tento odkaz odešle ID vybraného instruktoru do metody `Index` a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="b7655-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="b7655-416">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b7655-417">Pokud má OfficeAssignment hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="b7655-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b7655-418">Klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="b7655-418">Click on the **Select** link.</span></span> <span data-ttu-id="b7655-419">Styl řádku se změní.</span><span class="sxs-lookup"><span data-stu-id="b7655-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="b7655-420">Přidat výukové kurzy podle vybraného instruktora</span><span class="sxs-lookup"><span data-stu-id="b7655-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="b7655-421">Aktualizujte metodu `OnGetAsync` na *stránkách/instruktorech/index. cshtml. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b7655-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="b7655-422">Přidat `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="b7655-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="b7655-423">Projděte si aktualizovaný dotaz:</span><span class="sxs-lookup"><span data-stu-id="b7655-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b7655-424">Předchozí dotaz přidá entity `Department`.</span><span class="sxs-lookup"><span data-stu-id="b7655-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="b7655-425">Následující kód se spustí, když je vybrán instruktor (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="b7655-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="b7655-426">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b7655-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b7655-427">Vlastnost `Courses` modelu zobrazení je načtena s `Course` entit z `CourseAssignments` navigační vlastnost tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="b7655-428">Metoda `Where` vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="b7655-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="b7655-429">V předchozí metodě `Where` je vrácena pouze jediná entita `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b7655-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="b7655-430">Metoda `Single` převede kolekci na jednu entitu `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="b7655-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b7655-431">Entita `Instructor` poskytuje přístup k vlastnosti `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="b7655-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b7655-432">`CourseAssignments` poskytuje přístup k souvisejícím entitám `Course`.</span><span class="sxs-lookup"><span data-stu-id="b7655-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b7655-434">Metoda `Single` se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="b7655-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b7655-435">Metoda `Single` vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="b7655-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b7655-436">Alternativa je `SingleOrDefault`, která vrací výchozí hodnotu (v tomto případě hodnotu null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="b7655-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="b7655-437">Použití `SingleOrDefault` v prázdné kolekci:</span><span class="sxs-lookup"><span data-stu-id="b7655-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="b7655-438">Výsledkem je výjimka (při pokusu o nalezení vlastnosti `Courses` v odkazu s hodnotou null).</span><span class="sxs-lookup"><span data-stu-id="b7655-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="b7655-439">Zpráva o výjimce by byla zřetelně označovat příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="b7655-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="b7655-440">Následující kód naplní vlastnost `Enrollments` modelu zobrazení, když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="b7655-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="b7655-441">Na konec stránky */instruktory/index. cshtml* Razor přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="b7655-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="b7655-442">Předchozí kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="b7655-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="b7655-443">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7655-443">Test the app.</span></span> <span data-ttu-id="b7655-444">Na stránce instruktoři klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="b7655-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="b7655-445">Zobrazit data studenta</span><span class="sxs-lookup"><span data-stu-id="b7655-445">Show student data</span></span>

<span data-ttu-id="b7655-446">V této části se aplikace aktualizuje, aby zobrazovala údaje o studentech pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b7655-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="b7655-447">Aktualizujte dotaz v metodě `OnGetAsync` na *stránce Pages/instruktors/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7655-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b7655-448">Aktualizovat *stránky/instruktory/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b7655-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="b7655-449">Na konec souboru přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="b7655-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="b7655-450">Předchozí kód zobrazuje seznam studentů, kteří jsou zaregistrovaní ve vybraném kurzu.</span><span class="sxs-lookup"><span data-stu-id="b7655-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="b7655-451">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="b7655-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="b7655-452">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="b7655-452">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="b7655-454">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="b7655-454">Using Single</span></span>

<span data-ttu-id="b7655-455">Metoda `Single` může předat podmínku `Where` namísto volání metody `Where` samostatně:</span><span class="sxs-lookup"><span data-stu-id="b7655-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b7655-456">Předchozí přístup `Single` neposkytuje oproti použití `Where`žádné výhody.</span><span class="sxs-lookup"><span data-stu-id="b7655-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="b7655-457">Někteří vývojáři dávají přednost stylu `Single` přístupu.</span><span class="sxs-lookup"><span data-stu-id="b7655-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b7655-458">explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="b7655-458">Explicit loading</span></span>

<span data-ttu-id="b7655-459">Aktuální kód určuje Eager načítání pro `Enrollments` a `Students`:</span><span class="sxs-lookup"><span data-stu-id="b7655-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b7655-460">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="b7655-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b7655-461">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="b7655-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b7655-462">V této části je `OnGetAsync` aktualizován tak, aby používal explicitní načítání `Enrollments` a `Students`.</span><span class="sxs-lookup"><span data-stu-id="b7655-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b7655-463">Aktualizujte `OnGetAsync` s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7655-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="b7655-464">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="b7655-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b7655-465">Pokud je vybrán kurz, zvýrazněný kód načte:</span><span class="sxs-lookup"><span data-stu-id="b7655-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="b7655-466">Entity `Enrollment` pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b7655-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b7655-467">`Student` entit pro každý `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b7655-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b7655-468">Všimněte si předcházejících komentářů k kódu `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="b7655-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b7655-469">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="b7655-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b7655-470">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7655-470">Test the app.</span></span> <span data-ttu-id="b7655-471">V perspektivě uživatelů se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="b7655-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="b7655-472">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="b7655-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7655-473">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b7655-473">Additional resources</span></span>

* [<span data-ttu-id="b7655-474">Verze tohoto kurzu pro YouTube (part1)</span><span class="sxs-lookup"><span data-stu-id="b7655-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="b7655-475">Verze tohoto kurzu pro YouTube (Část2)</span><span class="sxs-lookup"><span data-stu-id="b7655-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="b7655-476">[Předchozí](xref:data/ef-rp/complex-data-model)
>[Další](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b7655-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
