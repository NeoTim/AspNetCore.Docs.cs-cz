---
title: Razor Pages s EF Core ve ASP.NET Core – data související s čtením – 6 z 8
author: tdykstra
description: V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která Entity Framework načíst do vlastností navigace.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: c9da404b1bbd072d3e033f18a7366169082dac06
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583544"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="a0217-103">Razor Pages s EF Core ve ASP.NET Core – data související s čtením – 6 z 8</span><span class="sxs-lookup"><span data-stu-id="a0217-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="a0217-104">Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a0217-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0217-105">V tomto kurzu se dozvíte, jak číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="a0217-106">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="a0217-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="a0217-107">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="a0217-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="a0217-110">Eager, explicitní a opožděné načítání</span><span class="sxs-lookup"><span data-stu-id="a0217-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="a0217-111">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="a0217-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="a0217-112">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="a0217-113">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="a0217-114">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="a0217-115">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="a0217-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="a0217-116">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="a0217-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="a0217-117">Vystavení více dotazů může být efektivnější než obří jediný dotaz.</span><span class="sxs-lookup"><span data-stu-id="a0217-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="a0217-118">Eager načítání je zadáno pomocí `Include` metod a. `ThenInclude`</span><span class="sxs-lookup"><span data-stu-id="a0217-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="a0217-120">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="a0217-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="a0217-121">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="a0217-121">One query for the main query</span></span> 
  * <span data-ttu-id="a0217-122">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="a0217-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="a0217-123">Oddělte dotazy `Load`pomocí: Data lze načíst v samostatných dotazech a EF Core "opravuje" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="a0217-124">"Opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="a0217-125">Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="a0217-127">Poznámka: EF Core automaticky opravuje navigační vlastnosti na jakékoli další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="a0217-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="a0217-128">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="a0217-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="a0217-129">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="a0217-130">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="a0217-131">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="a0217-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="a0217-132">Explicitní načítání pomocí samostatných dotazů má za následek odeslání více dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="a0217-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="a0217-133">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="a0217-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="a0217-134">K provedení explicitního načítání použijte metodu.`Load`</span><span class="sxs-lookup"><span data-stu-id="a0217-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="a0217-135">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a0217-135">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="a0217-137">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="a0217-138">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="a0217-139">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="a0217-140">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="a0217-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="a0217-141">Dotaz je odeslán do databáze při každém prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="a0217-142">Vytvořit stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="a0217-142">Create Course pages</span></span>

<span data-ttu-id="a0217-143">Entita obsahuje navigační vlastnost, která obsahuje související `Department` entitu. `Course`</span><span class="sxs-lookup"><span data-stu-id="a0217-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<span data-ttu-id="a0217-145">Chcete-li zobrazit název přiřazeného oddělení za kurz:</span><span class="sxs-lookup"><span data-stu-id="a0217-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="a0217-146">Načtěte související `Department` entitu `Course.Department` do vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="a0217-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="a0217-147">Získá název z `Department` `Name` vlastnosti entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="a0217-148">Stránky kurzu generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="a0217-148">Scaffold Course pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0217-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0217-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0217-150">Postupujte podle pokynů v části [stránky](xref:data/ef-rp/intro#scaffold-student-pages) pro studenty s těmito výjimkami:</span><span class="sxs-lookup"><span data-stu-id="a0217-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="a0217-151">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="a0217-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="a0217-152">Použijte `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="a0217-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="a0217-153">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="a0217-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0217-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0217-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0217-155">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="a0217-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="a0217-156">Spusťte následující příkaz pro generování uživatelského rozhraní stránek kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0217-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="a0217-157">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="a0217-157">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="a0217-158">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="a0217-158">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="a0217-159">Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="a0217-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="a0217-160">Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="a0217-161">`Include` Metoda určuje Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="a0217-162">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="a0217-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="a0217-163">Sloupec oddělení zobrazí `DepartmentID`, což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="a0217-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="a0217-164">Zobrazit název oddělení</span><span class="sxs-lookup"><span data-stu-id="a0217-164">Display the department name</span></span>

<span data-ttu-id="a0217-165">Aktualizujte stránky/kurzy/index. cshtml. cs s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0217-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="a0217-166">Předchozí kód změní `Course` vlastnost na `Courses` a přidá `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="a0217-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="a0217-167">`AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="a0217-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="a0217-168">Entity není nutné sledovat, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="a0217-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="a0217-169">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="a0217-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="a0217-170">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="a0217-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="a0217-171">Změnila se název `Courses` vlastnostina.`Course`</span><span class="sxs-lookup"><span data-stu-id="a0217-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="a0217-172">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="a0217-173">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="a0217-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="a0217-174">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="a0217-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="a0217-175">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0217-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="a0217-176">Kód zobrazí `Name` vlastnost `Department` entity `Department` , která je načtena do navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a0217-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="a0217-177">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0217-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="a0217-179">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="a0217-179">Loading related data with Select</span></span>

<span data-ttu-id="a0217-180">Metoda načte související data `Include` pomocí metody. `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="a0217-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="a0217-181">`Select` Metoda je alternativou, která načte pouze související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="a0217-182">Pro jednotlivé položky, `Department.Name` například používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="a0217-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="a0217-183">U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.</span><span class="sxs-lookup"><span data-stu-id="a0217-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="a0217-184">Následující kód načte související data `Select` metodou:</span><span class="sxs-lookup"><span data-stu-id="a0217-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="a0217-185">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="a0217-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="a0217-186">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="a0217-186">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="a0217-187">Vytvořit stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="a0217-187">Create Instructor pages</span></span>

<span data-ttu-id="a0217-188">Tato část vygeneruje stránky instruktora a přidá související kurzy a registrace na stránku indexu instruktorů.</span><span class="sxs-lookup"><span data-stu-id="a0217-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="a0217-189">![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="a0217-189">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="a0217-190">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="a0217-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="a0217-191">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="a0217-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="a0217-192">Entity `Instructor` a`OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota.</span><span class="sxs-lookup"><span data-stu-id="a0217-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="a0217-193">Pro `OfficeAssignment` entity se používá Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="a0217-194">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="a0217-195">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="a0217-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="a0217-196">Když uživatel vybere instruktora, zobrazí se `Course` související entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="a0217-197">Entity `Instructor` a`Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="a0217-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="a0217-198">Eager načítání se používá pro `Course` entity a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="a0217-199">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="a0217-200">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="a0217-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="a0217-201">Když uživatel vybere kurz, zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="a0217-202">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="a0217-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="a0217-203">Entity `Course` a`Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="a0217-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="a0217-204">Vytvoření modelu zobrazení</span><span class="sxs-lookup"><span data-stu-id="a0217-204">Create a view model</span></span>

<span data-ttu-id="a0217-205">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="a0217-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="a0217-206">Model zobrazení je potřeba, který obsahuje tři vlastnosti reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="a0217-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="a0217-207">Vytvořte *SchoolViewModels/InstructorIndexData. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0217-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="a0217-208">Stránky instruktora uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="a0217-208">Scaffold Instructor pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0217-209">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0217-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0217-210">Postupujte podle pokynů v [části generování uživatelského rozhraní se stránkami studenta](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:</span><span class="sxs-lookup"><span data-stu-id="a0217-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="a0217-211">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="a0217-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="a0217-212">Použijte `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="a0217-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="a0217-213">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="a0217-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0217-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0217-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0217-215">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="a0217-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="a0217-216">Spusťte následující příkaz pro generování uživatelského rozhraní stránek instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="a0217-217">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="a0217-217">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="a0217-218">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="a0217-218">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="a0217-219">Chcete-li zjistit, jaké vygenerované stránky vypadá před tím, než je aktualizujete, spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="a0217-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="a0217-220">Aktualizovat *stránky/instruktory/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0217-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="a0217-221">`OnGetAsync` Metoda přijímá volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="a0217-222">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a0217-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="a0217-223">Kód určuje načítání Eager pro následující navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a0217-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="a0217-224">Všimněte si opakování `Include` a `ThenInclude` metod pro `CourseAssignments` a `Course`.</span><span class="sxs-lookup"><span data-stu-id="a0217-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="a0217-225">Toto opakování je nezbytné pro zadání Eager načítání pro dvě navigační vlastnosti `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="a0217-226">Následující kód se spustí při výběru instruktora (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="a0217-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="a0217-227">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a0217-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="a0217-228">`Courses` Vlastnost modelu zobrazení je načtena `Course` s `CourseAssignments` entitami z vlastnosti navigace tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="a0217-229">`Where` Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="a0217-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="a0217-230">Ale v tomto případě filtr vybere jednu entitu.</span><span class="sxs-lookup"><span data-stu-id="a0217-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="a0217-231">Proto je volána `Instructor` metodapropřevodkolekcena`Single` jedinou entitu.</span><span class="sxs-lookup"><span data-stu-id="a0217-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="a0217-232">Entita poskytuje přístup `CourseAssignments` k vlastnosti. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="a0217-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="a0217-233">`CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.</span><span class="sxs-lookup"><span data-stu-id="a0217-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="a0217-235">`Single` Metoda se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="a0217-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="a0217-236">`Single` Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="a0217-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="a0217-237">Alternativa je `SingleOrDefault`, která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="a0217-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="a0217-238">Následující kód naplní `Enrollments` vlastnost modelu zobrazení, když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="a0217-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="a0217-239">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="a0217-239">Update the instructors Index page</span></span>

<span data-ttu-id="a0217-240">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="a0217-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="a0217-241">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="a0217-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="a0217-242">Aktualizace `page` direktiv z `@page` k `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="a0217-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="a0217-243">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="a0217-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="a0217-244">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="a0217-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="a0217-245">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze direktivu `@page` , se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="a0217-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="a0217-246">V případě direktivy `@page "{id:int?}"`stránky je adresa URL:</span><span class="sxs-lookup"><span data-stu-id="a0217-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="a0217-247">Přidá sloupec **Office** , který se `item.OfficeAssignment.Location` zobrazí pouze `item.OfficeAssignment` v případě, že není null.</span><span class="sxs-lookup"><span data-stu-id="a0217-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="a0217-248">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="a0217-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="a0217-249">Přidá sloupec **kurzů** , ve kterém se zobrazují kurzy, které každý instruktor prodlužuje.</span><span class="sxs-lookup"><span data-stu-id="a0217-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="a0217-250">Další informace o této syntaxi Razor naleznete v tématu [ `@:` explicitní přechod řádků](xref:mvc/views/razor#explicit-line-transition-with-) .</span><span class="sxs-lookup"><span data-stu-id="a0217-250">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="a0217-251">Přidá kód, který dynamicky `class="success"` přidá `tr` do prvku vybraného instruktora a kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0217-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="a0217-252">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="a0217-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="a0217-253">Přidá nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="a0217-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="a0217-254">Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="a0217-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="a0217-255">Přidá tabulku kurzů pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="a0217-256">Přidá tabulku zápisů studentů pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="a0217-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="a0217-257">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="a0217-258">Pokud `OfficeAssignment` má hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="a0217-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="a0217-259">Klikněte na odkaz **Vybrat** pro instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="a0217-260">Zobrazí se změna stylu řádku a kurzy přiřazené k tomuto instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="a0217-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="a0217-261">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="a0217-261">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="a0217-263">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="a0217-263">Using Single</span></span>

<span data-ttu-id="a0217-264">Metoda může `Where` předat`Where` podmínku namísto volání metody samostatně: `Single`</span><span class="sxs-lookup"><span data-stu-id="a0217-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="a0217-265">`Single` Použití s podmínkou WHERE je záležitostí osobní preference.</span><span class="sxs-lookup"><span data-stu-id="a0217-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="a0217-266">Neposkytuje za použití `Where` metody žádné výhody.</span><span class="sxs-lookup"><span data-stu-id="a0217-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="a0217-267">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="a0217-267">Explicit loading</span></span>

<span data-ttu-id="a0217-268">Aktuální kód určuje Eager načítání pro `Enrollments` a: `Students`</span><span class="sxs-lookup"><span data-stu-id="a0217-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="a0217-269">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="a0217-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="a0217-270">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="a0217-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="a0217-271">V této části `OnGetAsync` je aktualizována pro použití explicitního `Enrollments` načítání a `Students`.</span><span class="sxs-lookup"><span data-stu-id="a0217-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="a0217-272">Aktualizujte *stránky/instruktory/index. cshtml. cs* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="a0217-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="a0217-273">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="a0217-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="a0217-274">Pokud je vybrán kurz, explicitní načítání kódu načte:</span><span class="sxs-lookup"><span data-stu-id="a0217-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="a0217-275">`Enrollment` Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="a0217-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="a0217-276">Entity pro každou `Enrollment`. `Student`</span><span class="sxs-lookup"><span data-stu-id="a0217-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="a0217-277">Všimněte si, že předchozí komentáře k `.AsNoTracking()`kódu byly vycházející.</span><span class="sxs-lookup"><span data-stu-id="a0217-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="a0217-278">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="a0217-279">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a0217-279">Test the app.</span></span> <span data-ttu-id="a0217-280">V perspektivě uživatele se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="a0217-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a0217-281">Další postup</span><span class="sxs-lookup"><span data-stu-id="a0217-281">Next steps</span></span>

<span data-ttu-id="a0217-282">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="a0217-283">[Předchozí kurz](xref:data/ef-rp/complex-data-model)
>–[Další kurz](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="a0217-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0217-284">V tomto kurzu se budou číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="a0217-285">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="a0217-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="a0217-286">Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="a0217-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="a0217-287">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a0217-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="a0217-288">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="a0217-288">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="a0217-291">Eager, explicitní a opožděné načítání souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="a0217-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="a0217-292">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="a0217-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="a0217-293">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="a0217-294">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="a0217-295">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="a0217-296">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="a0217-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="a0217-297">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="a0217-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="a0217-298">Vystavení více dotazů může být efektivnější než u některých dotazů v EF6, kde existoval jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="a0217-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="a0217-299">Eager načítání je zadáno pomocí `Include` metod a. `ThenInclude`</span><span class="sxs-lookup"><span data-stu-id="a0217-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="a0217-301">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="a0217-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="a0217-302">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="a0217-302">One query for the main query</span></span> 
  * <span data-ttu-id="a0217-303">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="a0217-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="a0217-304">Oddělte dotazy `Load`pomocí: Data lze načíst v samostatných dotazech a EF Core "opravuje" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="a0217-305">"opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="a0217-306">Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="a0217-308">Poznámka: EF Core automaticky opravuje navigační vlastnosti na jakékoli další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="a0217-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="a0217-309">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="a0217-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="a0217-310">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="a0217-311">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="a0217-312">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="a0217-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="a0217-313">Explicitní načítání pomocí samostatných dotazů má za následek více dotazů odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="a0217-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="a0217-314">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="a0217-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="a0217-315">K provedení explicitního načítání použijte metodu.`Load`</span><span class="sxs-lookup"><span data-stu-id="a0217-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="a0217-316">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a0217-316">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="a0217-318">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="a0217-319">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="a0217-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="a0217-320">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="a0217-321">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="a0217-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="a0217-322">Dotaz se pošle do databáze při prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="a0217-323">`Select` Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="a0217-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="a0217-324">Vytvoří stránku kurzu, která zobrazuje název oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0217-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="a0217-325">Entita kurzu obsahuje navigační vlastnost, která obsahuje `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="a0217-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="a0217-326">`Department` Entita obsahuje oddělení, ke kterému je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="a0217-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="a0217-327">Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů:</span><span class="sxs-lookup"><span data-stu-id="a0217-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="a0217-328">`Name` Získá vlastnost`Department` z entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="a0217-329">`Department` Entita pochází`Course.Department` z navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="a0217-331">Generování uživatelského rozhraní modelu kurzu</span><span class="sxs-lookup"><span data-stu-id="a0217-331">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0217-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0217-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a0217-333">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-student-pages) a použít `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="a0217-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Course` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0217-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0217-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="a0217-335">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a0217-335">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="a0217-336">Předchozí příkaz scaffold `Course` modelu.</span><span class="sxs-lookup"><span data-stu-id="a0217-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="a0217-337">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a0217-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="a0217-338">Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="a0217-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="a0217-339">Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="a0217-340">`Include` Metoda určuje Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="a0217-341">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="a0217-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="a0217-342">Sloupec oddělení zobrazí `DepartmentID`, což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="a0217-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="a0217-343">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0217-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="a0217-344">Předchozí kód přidá `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="a0217-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="a0217-345">`AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="a0217-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="a0217-346">Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="a0217-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="a0217-347">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="a0217-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="a0217-348">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="a0217-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="a0217-349">Změnili jste záhlaví z indexu na kurzy.</span><span class="sxs-lookup"><span data-stu-id="a0217-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="a0217-350">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a0217-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="a0217-351">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="a0217-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="a0217-352">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="a0217-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="a0217-353">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0217-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="a0217-354">Kód zobrazí `Name` vlastnost `Department` entity `Department` , která je načtena do navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a0217-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="a0217-355">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0217-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="a0217-357">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="a0217-357">Loading related data with Select</span></span>

<span data-ttu-id="a0217-358">Metoda načte související data `Include` pomocí metody: `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="a0217-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="a0217-359">`Select` Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="a0217-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="a0217-360">Pro jednotlivé položky, `Department.Name` například používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="a0217-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="a0217-361">U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.</span><span class="sxs-lookup"><span data-stu-id="a0217-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="a0217-362">Následující kód načte související data `Select` metodou:</span><span class="sxs-lookup"><span data-stu-id="a0217-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="a0217-363">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="a0217-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="a0217-364">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="a0217-364">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="a0217-365">Vytvoření stránky instruktory, která zobrazuje kurzy a registrace</span><span class="sxs-lookup"><span data-stu-id="a0217-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="a0217-366">V této části se vytvoří stránka instruktoři.</span><span class="sxs-lookup"><span data-stu-id="a0217-366">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="a0217-367">![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="a0217-367">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="a0217-368">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="a0217-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="a0217-369">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="a0217-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="a0217-370">Entity `Instructor` a`OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota.</span><span class="sxs-lookup"><span data-stu-id="a0217-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="a0217-371">Pro `OfficeAssignment` entity se používá Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="a0217-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="a0217-372">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="a0217-373">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="a0217-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="a0217-374">Když uživatel vybere instruktora (Harui na předchozím obrázku), zobrazí se související `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="a0217-375">Entity `Instructor` a`Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="a0217-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="a0217-376">Eager načítání se používá pro `Course` entity a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="a0217-377">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="a0217-378">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="a0217-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="a0217-379">Když uživatel vybere kurz (chemie na předchozím obrázku), zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="a0217-380">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="a0217-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="a0217-381">Entity `Course` a`Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="a0217-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="a0217-382">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="a0217-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="a0217-383">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="a0217-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="a0217-384">Vytvoří se model zobrazení, který obsahuje tři entity reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="a0217-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="a0217-385">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0217-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="a0217-386">Generování uživatelského rozhraní modelu instruktor</span><span class="sxs-lookup"><span data-stu-id="a0217-386">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0217-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0217-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a0217-388">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-student-pages) a použít `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="a0217-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0217-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0217-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="a0217-390">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a0217-390">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="a0217-391">Předchozí příkaz scaffold `Instructor` modelu.</span><span class="sxs-lookup"><span data-stu-id="a0217-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="a0217-392">Spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="a0217-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="a0217-393">*Stránky/instruktořis/index. cshtml. cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0217-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="a0217-394">`OnGetAsync` Metoda přijímá volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="a0217-395">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="a0217-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="a0217-396">Dotaz obsahuje dvě:</span><span class="sxs-lookup"><span data-stu-id="a0217-396">The query has two includes:</span></span>

* <span data-ttu-id="a0217-397">`OfficeAssignment`: Zobrazuje se v [zobrazení instruktoři](#IP).</span><span class="sxs-lookup"><span data-stu-id="a0217-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="a0217-398">`CourseAssignments`: Který přináší výukové kurzy.</span><span class="sxs-lookup"><span data-stu-id="a0217-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="a0217-399">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="a0217-399">Update the instructors Index page</span></span>

<span data-ttu-id="a0217-400">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="a0217-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="a0217-401">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="a0217-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="a0217-402">Aktualizace `page` direktiv z `@page` k `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="a0217-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="a0217-403">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="a0217-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="a0217-404">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="a0217-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="a0217-405">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze direktivu `@page` , se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="a0217-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="a0217-406">Pokud je `@page "{id:int?}"`direktiva stránky, předchozí adresa URL:</span><span class="sxs-lookup"><span data-stu-id="a0217-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="a0217-407">Nadpis stránky jsou **instruktory**.</span><span class="sxs-lookup"><span data-stu-id="a0217-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="a0217-408">Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v `item.OfficeAssignment` případě, že není null.</span><span class="sxs-lookup"><span data-stu-id="a0217-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="a0217-409">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="a0217-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="a0217-410">Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem.</span><span class="sxs-lookup"><span data-stu-id="a0217-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="a0217-411">Další informace o této syntaxi Razor naleznete v tématu [ `@:` explicitní přechod řádků](xref:mvc/views/razor#explicit-line-transition-with-) .</span><span class="sxs-lookup"><span data-stu-id="a0217-411">See [Explicit line transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="a0217-412">Přidaný kód, který `class="success"` dynamicky přidá `tr` do prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="a0217-413">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="a0217-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="a0217-414">Byl přidán nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="a0217-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="a0217-415">Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="a0217-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="a0217-416">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="a0217-417">Pokud má OfficeAssignment hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="a0217-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="a0217-418">Klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="a0217-418">Click on the **Select** link.</span></span> <span data-ttu-id="a0217-419">Styl řádku se změní.</span><span class="sxs-lookup"><span data-stu-id="a0217-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="a0217-420">Přidat výukové kurzy podle vybraného instruktora</span><span class="sxs-lookup"><span data-stu-id="a0217-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="a0217-421">Aktualizujte metodu na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem: `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="a0217-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="a0217-422">Přidávání`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="a0217-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="a0217-423">Projděte si aktualizovaný dotaz:</span><span class="sxs-lookup"><span data-stu-id="a0217-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="a0217-424">Předchozí dotaz přidá `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="a0217-425">Následující kód se spustí při výběru instruktora (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="a0217-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="a0217-426">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a0217-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="a0217-427">`Courses` Vlastnost modelu zobrazení je načtena `Course` s `CourseAssignments` entitami z vlastnosti navigace tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="a0217-428">`Where` Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="a0217-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="a0217-429">V předchozí `Where` metodě se vrátí jenom jedna `Instructor` entita.</span><span class="sxs-lookup"><span data-stu-id="a0217-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="a0217-430">Metoda převede kolekci na jednu `Instructor` entitu. `Single`</span><span class="sxs-lookup"><span data-stu-id="a0217-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="a0217-431">Entita poskytuje přístup `CourseAssignments` k vlastnosti. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="a0217-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="a0217-432">`CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.</span><span class="sxs-lookup"><span data-stu-id="a0217-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="a0217-434">`Single` Metoda se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="a0217-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="a0217-435">`Single` Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="a0217-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="a0217-436">Alternativa je `SingleOrDefault`, která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="a0217-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="a0217-437">Použití `SingleOrDefault` v prázdné kolekci:</span><span class="sxs-lookup"><span data-stu-id="a0217-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="a0217-438">Výsledkem je výjimka (při pokusu o nalezení `Courses` vlastnosti v odkazu s hodnotou null).</span><span class="sxs-lookup"><span data-stu-id="a0217-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="a0217-439">Zpráva o výjimce by byla zřetelně označovat příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="a0217-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="a0217-440">Následující kód naplní `Enrollments` vlastnost modelu zobrazení, když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="a0217-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="a0217-441">Na konec stránky */instruktory/index. cshtml* Razor přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a0217-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="a0217-442">Předchozí kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="a0217-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="a0217-443">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a0217-443">Test the app.</span></span> <span data-ttu-id="a0217-444">Na stránce instruktoři klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="a0217-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="a0217-445">Zobrazit data studenta</span><span class="sxs-lookup"><span data-stu-id="a0217-445">Show student data</span></span>

<span data-ttu-id="a0217-446">V této části se aplikace aktualizuje, aby zobrazovala údaje o studentech pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="a0217-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="a0217-447">Aktualizujte dotaz v `OnGetAsync` metodě na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0217-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="a0217-448">Aktualizovat *stránky/instruktory/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a0217-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="a0217-449">Na konec souboru přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a0217-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="a0217-450">Předchozí kód zobrazuje seznam studentů, kteří jsou zaregistrovaní ve vybraném kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0217-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="a0217-451">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="a0217-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="a0217-452">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="a0217-452">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="a0217-454">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="a0217-454">Using Single</span></span>

<span data-ttu-id="a0217-455">Metoda může `Where` předat`Where` podmínku namísto volání metody samostatně: `Single`</span><span class="sxs-lookup"><span data-stu-id="a0217-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="a0217-456">Předchozí `Single` přístup neposkytuje oproti použití `Where`žádné výhody.</span><span class="sxs-lookup"><span data-stu-id="a0217-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="a0217-457">Někteří vývojáři upřednostňují `Single` styl přístupu.</span><span class="sxs-lookup"><span data-stu-id="a0217-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="a0217-458">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="a0217-458">Explicit loading</span></span>

<span data-ttu-id="a0217-459">Aktuální kód určuje Eager načítání pro `Enrollments` a: `Students`</span><span class="sxs-lookup"><span data-stu-id="a0217-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="a0217-460">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="a0217-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="a0217-461">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="a0217-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="a0217-462">V této části `OnGetAsync` je aktualizována pro použití explicitního `Enrollments` načítání a `Students`.</span><span class="sxs-lookup"><span data-stu-id="a0217-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="a0217-463">Aktualizujte `OnGetAsync` pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="a0217-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="a0217-464">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="a0217-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="a0217-465">Pokud je vybrán kurz, zvýrazněný kód načte:</span><span class="sxs-lookup"><span data-stu-id="a0217-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="a0217-466">`Enrollment` Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="a0217-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="a0217-467">Entity pro každou `Enrollment`. `Student`</span><span class="sxs-lookup"><span data-stu-id="a0217-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="a0217-468">Všimněte si předcházejících komentářů `.AsNoTracking()`k kódu.</span><span class="sxs-lookup"><span data-stu-id="a0217-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="a0217-469">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="a0217-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="a0217-470">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a0217-470">Test the app.</span></span> <span data-ttu-id="a0217-471">V perspektivě uživatelů se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="a0217-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="a0217-472">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="a0217-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0217-473">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a0217-473">Additional resources</span></span>

* [<span data-ttu-id="a0217-474">Verze tohoto kurzu pro YouTube (part1)</span><span class="sxs-lookup"><span data-stu-id="a0217-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="a0217-475">Verze tohoto kurzu pro YouTube (Část2)</span><span class="sxs-lookup"><span data-stu-id="a0217-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="a0217-476">[Předchozí](xref:data/ef-rp/complex-data-model)Další
>[](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="a0217-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end