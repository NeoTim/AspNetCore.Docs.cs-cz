---
title: Část 6, Razor stránky s EF Core v souvisejících datech ASP.NET Core pro čtení
author: rick-anderson
description: 6. část Razor stránek a Entity Framework řady kurzů.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e67738015f64ca7077c2f87a8f7eabe722aac9d8
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652614"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="918e5-103">Část 6, Razor stránky s EF Core v souvisejících datech ASP.NET Core pro čtení</span><span class="sxs-lookup"><span data-stu-id="918e5-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="918e5-104">Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="918e5-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="918e5-105">V tomto kurzu se dozvíte, jak číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="918e5-106">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="918e5-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="918e5-107">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="918e5-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="918e5-110">Eager, explicitní a opožděné načítání</span><span class="sxs-lookup"><span data-stu-id="918e5-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="918e5-111">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="918e5-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="918e5-112">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="918e5-113">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="918e5-114">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="918e5-115">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="918e5-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="918e5-116">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="918e5-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="918e5-117">Vystavení více dotazů může být efektivnější než obří jediný dotaz.</span><span class="sxs-lookup"><span data-stu-id="918e5-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="918e5-118">Eager načítání je zadáno pomocí `Include` metod a `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="918e5-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="918e5-120">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="918e5-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="918e5-121">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="918e5-121">One query for the main query</span></span> 
  * <span data-ttu-id="918e5-122">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="918e5-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="918e5-123">Samostatné dotazy `Load` : data lze načíst v samostatných dotazech a EF Core "" řeší "navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="918e5-124">"Opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="918e5-125">Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="918e5-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="918e5-127">Poznámka: EF Core automaticky opravuje navigační vlastnosti pro všechny další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="918e5-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="918e5-128">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="918e5-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="918e5-129">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="918e5-130">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="918e5-131">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="918e5-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="918e5-132">Explicitní načítání pomocí samostatných dotazů má za následek odeslání více dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="918e5-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="918e5-133">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="918e5-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="918e5-134">`Load`K provedení explicitního načítání použijte metodu.</span><span class="sxs-lookup"><span data-stu-id="918e5-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="918e5-135">Například:</span><span class="sxs-lookup"><span data-stu-id="918e5-135">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="918e5-137">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="918e5-138">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="918e5-139">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="918e5-140">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="918e5-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="918e5-141">Dotaz je odeslán do databáze při každém prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="918e5-142">Vytvořit stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="918e5-142">Create Course pages</span></span>

<span data-ttu-id="918e5-143">`Course`Entita obsahuje navigační vlastnost, která obsahuje související `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="918e5-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<span data-ttu-id="918e5-145">Chcete-li zobrazit název přiřazeného oddělení za kurz:</span><span class="sxs-lookup"><span data-stu-id="918e5-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="918e5-146">Načtěte související `Department` entitu do `Course.Department` vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="918e5-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="918e5-147">Získá název z `Department` `Name` vlastnosti entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="918e5-148">Stránky kurzu generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="918e5-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="918e5-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="918e5-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="918e5-150">Postupujte podle pokynů v části [stránky pro studenty](xref:data/ef-rp/intro#scaffold-student-pages) s těmito výjimkami:</span><span class="sxs-lookup"><span data-stu-id="918e5-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="918e5-151">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="918e5-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="918e5-152">Použijte `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="918e5-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="918e5-153">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="918e5-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="918e5-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="918e5-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="918e5-155">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="918e5-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="918e5-156">Spusťte následující příkaz pro generování uživatelského rozhraní stránek kurzu.</span><span class="sxs-lookup"><span data-stu-id="918e5-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="918e5-157">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="918e5-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="918e5-158">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="918e5-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="918e5-159">Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="918e5-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="918e5-160">Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="918e5-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="918e5-161">`Include`Metoda určuje Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="918e5-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="918e5-162">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="918e5-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="918e5-163">Sloupec oddělení zobrazí `DepartmentID` , což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="918e5-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="918e5-164">Zobrazit název oddělení</span><span class="sxs-lookup"><span data-stu-id="918e5-164">Display the department name</span></span>

<span data-ttu-id="918e5-165">Aktualizujte stránky/kurzy/index. cshtml. cs s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="918e5-166">Předchozí kód změní `Course` vlastnost na `Courses` a přidá `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="918e5-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="918e5-167">`AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="918e5-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="918e5-168">Entity není nutné sledovat, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="918e5-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="918e5-169">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="918e5-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="918e5-170">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="918e5-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="918e5-171">Změnila se `Course` název vlastnosti na `Courses` .</span><span class="sxs-lookup"><span data-stu-id="918e5-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="918e5-172">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="918e5-173">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="918e5-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="918e5-174">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="918e5-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="918e5-175">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="918e5-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="918e5-176">Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do `Department` navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="918e5-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="918e5-177">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="918e5-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="918e5-179">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="918e5-179">Loading related data with Select</span></span>

<span data-ttu-id="918e5-180">`OnGetAsync`Metoda načte související data pomocí `Include` metody.</span><span class="sxs-lookup"><span data-stu-id="918e5-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="918e5-181">`Select`Metoda je alternativou, která načte pouze související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="918e5-182">Pro jednotlivé položky, například `Department.Name` používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="918e5-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="918e5-183">U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.</span><span class="sxs-lookup"><span data-stu-id="918e5-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="918e5-184">Následující kód načte související data `Select` metodou:</span><span class="sxs-lookup"><span data-stu-id="918e5-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="918e5-185">Předchozí kód nevrací žádné typy entit, proto žádné sledování není provedeno.</span><span class="sxs-lookup"><span data-stu-id="918e5-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="918e5-186">Další informace o sledování EF naleznete v tématu [sledování vs. žádné dotazy sledování](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="918e5-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="918e5-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="918e5-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="918e5-188">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="918e5-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="918e5-189">Vytvořit stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="918e5-189">Create Instructor pages</span></span>

<span data-ttu-id="918e5-190">Tato část vygeneruje stránky instruktora a přidá související kurzy a registrace na stránku indexu instruktorů.</span><span class="sxs-lookup"><span data-stu-id="918e5-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="918e5-191">![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="918e5-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="918e5-192">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="918e5-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="918e5-193">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="918e5-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="918e5-194">`Instructor`Entity a `OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota.</span><span class="sxs-lookup"><span data-stu-id="918e5-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="918e5-195">Pro entity se používá Eager načítání `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="918e5-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="918e5-196">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="918e5-197">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="918e5-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="918e5-198">Když uživatel vybere instruktora, `Course` zobrazí se související entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="918e5-199">`Instructor`Entity a `Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="918e5-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="918e5-200">Eager načítání se používá pro `Course` entity a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="918e5-201">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="918e5-202">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="918e5-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="918e5-203">Když uživatel vybere kurz, zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="918e5-204">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="918e5-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="918e5-205">`Course`Entity a `Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="918e5-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="918e5-206">Vytvoření modelu zobrazení</span><span class="sxs-lookup"><span data-stu-id="918e5-206">Create a view model</span></span>

<span data-ttu-id="918e5-207">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="918e5-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="918e5-208">Model zobrazení je potřeba, který obsahuje tři vlastnosti reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="918e5-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="918e5-209">Vytvořte *SchoolViewModels/InstructorIndexData. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="918e5-210">Stránky instruktora uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="918e5-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="918e5-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="918e5-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="918e5-212">Postupujte podle pokynů v [části generování uživatelského rozhraní se stránkami studenta](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:</span><span class="sxs-lookup"><span data-stu-id="918e5-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="918e5-213">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="918e5-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="918e5-214">Použijte `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="918e5-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="918e5-215">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="918e5-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="918e5-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="918e5-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="918e5-217">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="918e5-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="918e5-218">Spusťte následující příkaz pro generování uživatelského rozhraní stránek instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="918e5-219">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="918e5-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="918e5-220">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="918e5-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="918e5-221">Chcete-li zjistit, jaké vygenerované stránky vypadá před tím, než je aktualizujete, spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="918e5-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="918e5-222">Aktualizovat *stránky/instruktory/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="918e5-223">`OnGetAsync`Metoda přijímá volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="918e5-224">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="918e5-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="918e5-225">Kód určuje načítání Eager pro následující navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="918e5-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="918e5-226">Všimněte si opakování `Include` a `ThenInclude` metod pro `CourseAssignments` a `Course` .</span><span class="sxs-lookup"><span data-stu-id="918e5-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="918e5-227">Toto opakování je nezbytné pro zadání Eager načítání pro dvě navigační vlastnosti `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="918e5-228">Následující kód se spustí při výběru instruktora ( `id != null` ).</span><span class="sxs-lookup"><span data-stu-id="918e5-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="918e5-229">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="918e5-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="918e5-230">Vlastnost modelu zobrazení `Courses` je načtena s `Course` entitami z vlastnosti navigace tohoto instruktora `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="918e5-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="918e5-231">`Where`Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="918e5-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="918e5-232">Ale v tomto případě filtr vybere jednu entitu.</span><span class="sxs-lookup"><span data-stu-id="918e5-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="918e5-233">Proto `Single` je volána metoda pro převod kolekce na jedinou `Instructor` entitu.</span><span class="sxs-lookup"><span data-stu-id="918e5-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="918e5-234">`Instructor`Entita poskytuje přístup k `CourseAssignments` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="918e5-235">`CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.</span><span class="sxs-lookup"><span data-stu-id="918e5-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="918e5-237">`Single`Metoda se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="918e5-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="918e5-238">`Single`Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="918e5-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="918e5-239">Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="918e5-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="918e5-240">Následující kód naplní vlastnost modelu zobrazení, `Enrollments` když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="918e5-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="918e5-241">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="918e5-241">Update the instructors Index page</span></span>

<span data-ttu-id="918e5-242">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="918e5-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="918e5-243">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="918e5-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="918e5-244">Aktualizuje `page` direktivu z `@page` na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="918e5-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="918e5-245">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="918e5-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="918e5-246">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="918e5-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="918e5-247">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze `@page` direktivu, se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="918e5-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="918e5-248">V případě direktivy stránky je `@page "{id:int?}"` Adresa URL:</span><span class="sxs-lookup"><span data-stu-id="918e5-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="918e5-249">Přidá sloupec **Office** , který se zobrazí pouze v případě, že `item.OfficeAssignment.Location` `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="918e5-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="918e5-250">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="918e5-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="918e5-251">Přidá sloupec **kurzů** , ve kterém se zobrazují kurzy, které každý instruktor prodlužuje.</span><span class="sxs-lookup"><span data-stu-id="918e5-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="918e5-252">Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="918e5-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="918e5-253">Přidá kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora a kurzu.</span><span class="sxs-lookup"><span data-stu-id="918e5-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="918e5-254">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="918e5-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="918e5-255">Přidá nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="918e5-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="918e5-256">Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="918e5-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="918e5-257">Přidá tabulku kurzů pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="918e5-258">Přidá tabulku zápisů studentů pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="918e5-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="918e5-259">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="918e5-260">Pokud `OfficeAssignment` má hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="918e5-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="918e5-261">Klikněte na odkaz **Vybrat** pro instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="918e5-262">Zobrazí se změna stylu řádku a kurzy přiřazené k tomuto instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="918e5-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="918e5-263">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="918e5-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="918e5-265">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="918e5-265">Using Single</span></span>

<span data-ttu-id="918e5-266">`Single`Metoda může předat `Where` podmínku namísto volání `Where` metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="918e5-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="918e5-267">Použití `Single` s podmínkou WHERE je záležitostí osobní preference.</span><span class="sxs-lookup"><span data-stu-id="918e5-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="918e5-268">Neposkytuje za použití metody žádné výhody `Where` .</span><span class="sxs-lookup"><span data-stu-id="918e5-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="918e5-269">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="918e5-269">Explicit loading</span></span>

<span data-ttu-id="918e5-270">Aktuální kód určuje Eager načítání pro `Enrollments` a `Students` :</span><span class="sxs-lookup"><span data-stu-id="918e5-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="918e5-271">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="918e5-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="918e5-272">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="918e5-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="918e5-273">V této části `OnGetAsync` je aktualizována pro použití explicitního načítání `Enrollments` a `Students` .</span><span class="sxs-lookup"><span data-stu-id="918e5-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="918e5-274">Aktualizujte *stránky/instruktory/index. cshtml. cs* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="918e5-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="918e5-275">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="918e5-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="918e5-276">Pokud je vybrán kurz, explicitní načítání kódu načte:</span><span class="sxs-lookup"><span data-stu-id="918e5-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="918e5-277">`Enrollment`Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="918e5-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="918e5-278">`Student`Entity pro každou `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="918e5-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="918e5-279">Všimněte si, že předchozí komentáře k kódu byly vycházející `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="918e5-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="918e5-280">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="918e5-281">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="918e5-281">Test the app.</span></span> <span data-ttu-id="918e5-282">V perspektivě uživatele se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="918e5-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="918e5-283">Další kroky</span><span class="sxs-lookup"><span data-stu-id="918e5-283">Next steps</span></span>

<span data-ttu-id="918e5-284">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="918e5-285">[Předchozí kurz](xref:data/ef-rp/complex-data-model) 
> [Další kurz](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="918e5-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="918e5-286">V tomto kurzu se budou číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="918e5-287">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="918e5-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="918e5-288">Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="918e5-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="918e5-289">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="918e5-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="918e5-290">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="918e5-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="918e5-293">Eager, explicitní a opožděné načítání souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="918e5-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="918e5-294">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="918e5-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="918e5-295">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="918e5-296">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="918e5-297">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="918e5-298">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="918e5-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="918e5-299">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="918e5-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="918e5-300">Vystavení více dotazů může být efektivnější než u některých dotazů v EF6, kde existoval jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="918e5-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="918e5-301">Eager načítání je zadáno pomocí `Include` metod a `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="918e5-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="918e5-303">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="918e5-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="918e5-304">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="918e5-304">One query for the main query</span></span> 
  * <span data-ttu-id="918e5-305">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="918e5-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="918e5-306">Samostatné dotazy `Load` : data lze načíst v samostatných dotazech a EF Core "" řeší "navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="918e5-307">"opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="918e5-308">Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="918e5-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="918e5-310">Poznámka: EF Core automaticky opravuje navigační vlastnosti pro všechny další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="918e5-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="918e5-311">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="918e5-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="918e5-312">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="918e5-313">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="918e5-314">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="918e5-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="918e5-315">Explicitní načítání pomocí samostatných dotazů má za následek více dotazů odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="918e5-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="918e5-316">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="918e5-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="918e5-317">`Load`K provedení explicitního načítání použijte metodu.</span><span class="sxs-lookup"><span data-stu-id="918e5-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="918e5-318">Například:</span><span class="sxs-lookup"><span data-stu-id="918e5-318">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="918e5-320">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="918e5-321">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="918e5-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="918e5-322">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="918e5-323">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="918e5-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="918e5-324">Dotaz se pošle do databáze při prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="918e5-325">`Select`Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="918e5-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="918e5-326">Vytvoří stránku kurzu, která zobrazuje název oddělení.</span><span class="sxs-lookup"><span data-stu-id="918e5-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="918e5-327">Entita kurzu obsahuje navigační vlastnost, která obsahuje `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="918e5-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="918e5-328">`Department`Entita obsahuje oddělení, ke kterému je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="918e5-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="918e5-329">Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů:</span><span class="sxs-lookup"><span data-stu-id="918e5-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="918e5-330">Získá `Name` vlastnost z `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="918e5-331">`Department`Entita pochází z `Course.Department` navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="918e5-333">Generování uživatelského rozhraní modelu kurzu</span><span class="sxs-lookup"><span data-stu-id="918e5-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="918e5-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="918e5-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="918e5-335">Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="918e5-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="918e5-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="918e5-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="918e5-337">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="918e5-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="918e5-338">Předchozí příkaz vygeneruje `Course` model.</span><span class="sxs-lookup"><span data-stu-id="918e5-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="918e5-339">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="918e5-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="918e5-340">Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="918e5-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="918e5-341">Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="918e5-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="918e5-342">`Include`Metoda určuje Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="918e5-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="918e5-343">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="918e5-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="918e5-344">Sloupec oddělení zobrazí `DepartmentID` , což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="918e5-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="918e5-345">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="918e5-346">Předchozí kód přidá `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="918e5-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="918e5-347">`AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="918e5-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="918e5-348">Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="918e5-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="918e5-349">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="918e5-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="918e5-350">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="918e5-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="918e5-351">Změnili jste záhlaví z indexu na kurzy.</span><span class="sxs-lookup"><span data-stu-id="918e5-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="918e5-352">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="918e5-353">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="918e5-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="918e5-354">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="918e5-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="918e5-355">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="918e5-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="918e5-356">Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do `Department` navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="918e5-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="918e5-357">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="918e5-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="918e5-359">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="918e5-359">Loading related data with Select</span></span>

<span data-ttu-id="918e5-360">`OnGetAsync`Metoda načte související data pomocí `Include` metody:</span><span class="sxs-lookup"><span data-stu-id="918e5-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="918e5-361">`Select`Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="918e5-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="918e5-362">Pro jednotlivé položky, například `Department.Name` používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="918e5-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="918e5-363">U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.</span><span class="sxs-lookup"><span data-stu-id="918e5-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="918e5-364">Následující kód načte související data `Select` metodou:</span><span class="sxs-lookup"><span data-stu-id="918e5-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="918e5-365">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="918e5-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="918e5-366">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="918e5-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="918e5-367">Vytvoření stránky instruktory, která zobrazuje kurzy a registrace</span><span class="sxs-lookup"><span data-stu-id="918e5-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="918e5-368">V této části se vytvoří stránka instruktoři.</span><span class="sxs-lookup"><span data-stu-id="918e5-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="918e5-369">![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="918e5-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="918e5-370">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="918e5-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="918e5-371">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="918e5-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="918e5-372">`Instructor`Entity a `OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota.</span><span class="sxs-lookup"><span data-stu-id="918e5-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="918e5-373">Pro entity se používá Eager načítání `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="918e5-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="918e5-374">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="918e5-375">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="918e5-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="918e5-376">Když uživatel vybere instruktora (Harui na předchozím obrázku), `Course` zobrazí se související entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="918e5-377">`Instructor`Entity a `Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="918e5-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="918e5-378">Eager načítání se používá pro `Course` entity a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="918e5-379">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="918e5-380">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="918e5-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="918e5-381">Když uživatel vybere kurz (chemie na předchozím obrázku), zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="918e5-382">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="918e5-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="918e5-383">`Course`Entity a `Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="918e5-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="918e5-384">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="918e5-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="918e5-385">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="918e5-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="918e5-386">Vytvoří se model zobrazení, který obsahuje tři entity reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="918e5-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="918e5-387">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="918e5-388">Generování uživatelského rozhraní modelu instruktor</span><span class="sxs-lookup"><span data-stu-id="918e5-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="918e5-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="918e5-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="918e5-390">Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="918e5-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="918e5-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="918e5-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="918e5-392">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="918e5-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="918e5-393">Předchozí příkaz vygeneruje `Instructor` model.</span><span class="sxs-lookup"><span data-stu-id="918e5-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="918e5-394">Spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="918e5-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="918e5-395">*Stránky/instruktořis/index. cshtml. cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="918e5-396">`OnGetAsync`Metoda přijímá volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="918e5-397">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="918e5-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="918e5-398">Dotaz obsahuje dvě:</span><span class="sxs-lookup"><span data-stu-id="918e5-398">The query has two includes:</span></span>

* <span data-ttu-id="918e5-399">`OfficeAssignment`: Zobrazí se v [zobrazení instruktory](#IP).</span><span class="sxs-lookup"><span data-stu-id="918e5-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="918e5-400">`CourseAssignments`: Přináší výuku kurzů.</span><span class="sxs-lookup"><span data-stu-id="918e5-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="918e5-401">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="918e5-401">Update the instructors Index page</span></span>

<span data-ttu-id="918e5-402">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="918e5-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="918e5-403">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="918e5-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="918e5-404">Aktualizuje `page` direktivu z `@page` na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="918e5-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="918e5-405">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="918e5-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="918e5-406">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="918e5-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="918e5-407">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze `@page` direktivu, se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="918e5-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="918e5-408">Pokud je direktiva stránky `@page "{id:int?}"` , předchozí adresa URL:</span><span class="sxs-lookup"><span data-stu-id="918e5-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="918e5-409">Nadpis stránky jsou **instruktory**.</span><span class="sxs-lookup"><span data-stu-id="918e5-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="918e5-410">Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v případě, že `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="918e5-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="918e5-411">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="918e5-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="918e5-412">Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem.</span><span class="sxs-lookup"><span data-stu-id="918e5-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="918e5-413">Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="918e5-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="918e5-414">Přidaný kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="918e5-415">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="918e5-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="918e5-416">Byl přidán nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="918e5-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="918e5-417">Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="918e5-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="918e5-418">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="918e5-419">Pokud má OfficeAssignment hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="918e5-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="918e5-420">Klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="918e5-420">Click on the **Select** link.</span></span> <span data-ttu-id="918e5-421">Styl řádku se změní.</span><span class="sxs-lookup"><span data-stu-id="918e5-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="918e5-422">Přidat výukové kurzy podle vybraného instruktora</span><span class="sxs-lookup"><span data-stu-id="918e5-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="918e5-423">Aktualizujte `OnGetAsync` metodu na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="918e5-424">Přidávání`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="918e5-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="918e5-425">Projděte si aktualizovaný dotaz:</span><span class="sxs-lookup"><span data-stu-id="918e5-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="918e5-426">Předchozí dotaz přidá `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="918e5-427">Následující kód se spustí při výběru instruktora ( `id != null` ).</span><span class="sxs-lookup"><span data-stu-id="918e5-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="918e5-428">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="918e5-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="918e5-429">Vlastnost modelu zobrazení `Courses` je načtena s `Course` entitami z vlastnosti navigace tohoto instruktora `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="918e5-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="918e5-430">`Where`Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="918e5-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="918e5-431">V předchozí `Where` metodě `Instructor` se vrátí jenom jedna entita.</span><span class="sxs-lookup"><span data-stu-id="918e5-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="918e5-432">`Single`Metoda převede kolekci na jednu `Instructor` entitu.</span><span class="sxs-lookup"><span data-stu-id="918e5-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="918e5-433">`Instructor`Entita poskytuje přístup k `CourseAssignments` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="918e5-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="918e5-434">`CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.</span><span class="sxs-lookup"><span data-stu-id="918e5-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="918e5-436">`Single`Metoda se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="918e5-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="918e5-437">`Single`Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="918e5-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="918e5-438">Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="918e5-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="918e5-439">Použití `SingleOrDefault` v prázdné kolekci:</span><span class="sxs-lookup"><span data-stu-id="918e5-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="918e5-440">Výsledkem je výjimka (při pokusu o nalezení `Courses` vlastnosti v odkazu s hodnotou null).</span><span class="sxs-lookup"><span data-stu-id="918e5-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="918e5-441">Zpráva o výjimce by byla zřetelně označovat příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="918e5-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="918e5-442">Následující kód naplní vlastnost modelu zobrazení, `Enrollments` když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="918e5-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="918e5-443">Na konec stránky *stránky/instruktory/index. cshtml* přidejte následující kód Razor :</span><span class="sxs-lookup"><span data-stu-id="918e5-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="918e5-444">Předchozí kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="918e5-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="918e5-445">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="918e5-445">Test the app.</span></span> <span data-ttu-id="918e5-446">Na stránce instruktoři klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="918e5-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="918e5-447">Zobrazit data studenta</span><span class="sxs-lookup"><span data-stu-id="918e5-447">Show student data</span></span>

<span data-ttu-id="918e5-448">V této části se aplikace aktualizuje, aby zobrazovala údaje o studentech pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="918e5-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="918e5-449">Aktualizujte dotaz v `OnGetAsync` metodě na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="918e5-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="918e5-450">Aktualizovat *stránky/instruktory/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="918e5-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="918e5-451">Na konec souboru přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="918e5-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="918e5-452">Předchozí kód zobrazuje seznam studentů, kteří jsou zaregistrovaní ve vybraném kurzu.</span><span class="sxs-lookup"><span data-stu-id="918e5-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="918e5-453">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="918e5-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="918e5-454">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="918e5-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="918e5-456">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="918e5-456">Using Single</span></span>

<span data-ttu-id="918e5-457">`Single`Metoda může předat `Where` podmínku namísto volání `Where` metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="918e5-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="918e5-458">Předchozí `Single` přístup neposkytuje oproti použití žádné výhody `Where` .</span><span class="sxs-lookup"><span data-stu-id="918e5-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="918e5-459">Někteří vývojáři upřednostňují `Single` styl přístupu.</span><span class="sxs-lookup"><span data-stu-id="918e5-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="918e5-460">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="918e5-460">Explicit loading</span></span>

<span data-ttu-id="918e5-461">Aktuální kód určuje Eager načítání pro `Enrollments` a `Students` :</span><span class="sxs-lookup"><span data-stu-id="918e5-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="918e5-462">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="918e5-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="918e5-463">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="918e5-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="918e5-464">V této části `OnGetAsync` je aktualizována pro použití explicitního načítání `Enrollments` a `Students` .</span><span class="sxs-lookup"><span data-stu-id="918e5-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="918e5-465">Aktualizujte `OnGetAsync` pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="918e5-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="918e5-466">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="918e5-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="918e5-467">Pokud je vybrán kurz, zvýrazněný kód načte:</span><span class="sxs-lookup"><span data-stu-id="918e5-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="918e5-468">`Enrollment`Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="918e5-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="918e5-469">`Student`Entity pro každou `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="918e5-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="918e5-470">Všimněte si předcházejících komentářů k kódu `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="918e5-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="918e5-471">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="918e5-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="918e5-472">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="918e5-472">Test the app.</span></span> <span data-ttu-id="918e5-473">V perspektivě uživatelů se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="918e5-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="918e5-474">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="918e5-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="918e5-475">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="918e5-475">Additional resources</span></span>

* [<span data-ttu-id="918e5-476">Verze tohoto kurzu pro YouTube (part1)</span><span class="sxs-lookup"><span data-stu-id="918e5-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="918e5-477">Verze tohoto kurzu pro YouTube (Část2)</span><span class="sxs-lookup"><span data-stu-id="918e5-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="918e5-478">[Předchozí](xref:data/ef-rp/complex-data-model) 
> [Další](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="918e5-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
