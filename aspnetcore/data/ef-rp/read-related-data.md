---
title: Část 6, Razor stránky s EF Core v souvisejících datech ASP.NET Core pro čtení
author: rick-anderson
description: 6. část Razor stránek a Entity Framework řady kurzů.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 171607544bfe89fdd0a1ed9efb68f7a532f9aee1
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212655"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="b8276-103">Část 6, Razor stránky s EF Core v souvisejících datech ASP.NET Core pro čtení</span><span class="sxs-lookup"><span data-stu-id="b8276-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="b8276-104">Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b8276-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b8276-105">V tomto kurzu se dozvíte, jak číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="b8276-106">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="b8276-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b8276-107">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="b8276-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="b8276-110">Eager, explicitní a opožděné načítání</span><span class="sxs-lookup"><span data-stu-id="b8276-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="b8276-111">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="b8276-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b8276-112">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b8276-113">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b8276-114">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b8276-115">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="b8276-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b8276-116">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="b8276-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b8276-117">Vystavení více dotazů může být efektivnější než obří jediný dotaz.</span><span class="sxs-lookup"><span data-stu-id="b8276-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="b8276-118">Eager načítání je zadáno pomocí `Include` metod a `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="b8276-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b8276-120">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="b8276-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b8276-121">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="b8276-121">One query for the main query</span></span> 
  * <span data-ttu-id="b8276-122">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="b8276-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b8276-123">Samostatné dotazy `Load` : data lze načíst v samostatných dotazech a EF Core "" řeší "navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b8276-124">"Opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b8276-125">Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b8276-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b8276-127">**Poznámka:** EF Core automaticky opravuje navigační vlastnosti na jakékoli další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="b8276-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b8276-128">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="b8276-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b8276-129">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b8276-130">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b8276-131">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="b8276-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b8276-132">Explicitní načítání pomocí samostatných dotazů má za následek odeslání více dotazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="b8276-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="b8276-133">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="b8276-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b8276-134">`Load`K provedení explicitního načítání použijte metodu.</span><span class="sxs-lookup"><span data-stu-id="b8276-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b8276-135">Zde je příklad:</span><span class="sxs-lookup"><span data-stu-id="b8276-135">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b8276-137">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b8276-138">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b8276-139">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b8276-140">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="b8276-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b8276-141">Dotaz je odeslán do databáze při každém prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="b8276-142">Vytvořit stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="b8276-142">Create Course pages</span></span>

<span data-ttu-id="b8276-143">`Course`Entita obsahuje navigační vlastnost, která obsahuje související `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="b8276-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<span data-ttu-id="b8276-145">Chcete-li zobrazit název přiřazeného oddělení za kurz:</span><span class="sxs-lookup"><span data-stu-id="b8276-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="b8276-146">Načtěte související `Department` entitu do `Course.Department` vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="b8276-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="b8276-147">Získá název z `Department` `Name` vlastnosti entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="b8276-148">Stránky kurzu generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="b8276-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8276-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8276-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b8276-150">Postupujte podle pokynů v části [stránky pro studenty](xref:data/ef-rp/intro#scaffold-student-pages) s těmito výjimkami:</span><span class="sxs-lookup"><span data-stu-id="b8276-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b8276-151">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="b8276-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="b8276-152">Použijte `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b8276-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="b8276-153">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="b8276-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8276-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8276-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b8276-155">Vytvořte složku *stránky nebo kurzy* .</span><span class="sxs-lookup"><span data-stu-id="b8276-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="b8276-156">Spusťte následující příkaz pro generování uživatelského rozhraní stránek kurzu.</span><span class="sxs-lookup"><span data-stu-id="b8276-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="b8276-157">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="b8276-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="b8276-158">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="b8276-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="b8276-159">Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="b8276-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b8276-160">Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b8276-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b8276-161">`Include`Metoda určuje Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b8276-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="b8276-162">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="b8276-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b8276-163">Sloupec oddělení zobrazí `DepartmentID` , což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="b8276-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="b8276-164">Zobrazit název oddělení</span><span class="sxs-lookup"><span data-stu-id="b8276-164">Display the department name</span></span>

<span data-ttu-id="b8276-165">Aktualizujte stránky/kurzy/index. cshtml. cs s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="b8276-166">Předchozí kód změní `Course` vlastnost na `Courses` a přidá `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="b8276-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="b8276-167">`AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="b8276-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b8276-168">Entity není nutné sledovat, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="b8276-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b8276-169">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="b8276-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="b8276-170">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="b8276-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b8276-171">Změnila se `Course` název vlastnosti na `Courses` .</span><span class="sxs-lookup"><span data-stu-id="b8276-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="b8276-172">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b8276-173">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="b8276-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b8276-174">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="b8276-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b8276-175">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="b8276-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b8276-176">Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do `Department` navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b8276-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b8276-177">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="b8276-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b8276-179">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="b8276-179">Loading related data with Select</span></span>

<span data-ttu-id="b8276-180">`OnGetAsync`Metoda načte související data pomocí `Include` metody.</span><span class="sxs-lookup"><span data-stu-id="b8276-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="b8276-181">`Select`Metoda je alternativou, která načte pouze související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="b8276-182">Pro jednotlivé položky, například `Department.Name` používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="b8276-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b8276-183">U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.</span><span class="sxs-lookup"><span data-stu-id="b8276-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b8276-184">Následující kód načte související data `Select` metodou:</span><span class="sxs-lookup"><span data-stu-id="b8276-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="b8276-185">Předchozí kód nevrací žádné typy entit, proto žádné sledování není provedeno.</span><span class="sxs-lookup"><span data-stu-id="b8276-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="b8276-186">Další informace o sledování EF naleznete v tématu [sledování vs. žádné dotazy sledování](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="b8276-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="b8276-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b8276-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b8276-188">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="b8276-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="b8276-189">Vytvořit stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="b8276-189">Create Instructor pages</span></span>

<span data-ttu-id="b8276-190">Tato část vygeneruje stránky instruktora a přidá související kurzy a registrace na stránku indexu instruktorů.</span><span class="sxs-lookup"><span data-stu-id="b8276-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="b8276-191">![Stránka indexu instruktorů](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="b8276-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="b8276-192">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="b8276-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b8276-193">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="b8276-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b8276-194">`Instructor`Entity a `OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota.</span><span class="sxs-lookup"><span data-stu-id="b8276-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b8276-195">Pro entity se používá Eager načítání `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="b8276-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b8276-196">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b8276-197">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="b8276-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b8276-198">Když uživatel vybere instruktora, `Course` zobrazí se související entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="b8276-199">`Instructor`Entity a `Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="b8276-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b8276-200">Eager načítání se používá pro `Course` entity a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b8276-201">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b8276-202">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="b8276-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b8276-203">Když uživatel vybere kurz, zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b8276-204">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="b8276-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b8276-205">`Course`Entity a `Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="b8276-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="b8276-206">Vytvoření modelu zobrazení</span><span class="sxs-lookup"><span data-stu-id="b8276-206">Create a view model</span></span>

<span data-ttu-id="b8276-207">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="b8276-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b8276-208">Model zobrazení je potřeba, který obsahuje tři vlastnosti reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="b8276-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="b8276-209">Vytvořte *SchoolViewModels/InstructorIndexData. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="b8276-210">Stránky instruktora uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="b8276-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8276-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8276-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b8276-212">Postupujte podle pokynů v [části generování uživatelského rozhraní se stránkami studenta](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:</span><span class="sxs-lookup"><span data-stu-id="b8276-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="b8276-213">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="b8276-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="b8276-214">Použijte `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b8276-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="b8276-215">Místo vytvoření nové třídy kontextu použijte existující kontextovou třídu.</span><span class="sxs-lookup"><span data-stu-id="b8276-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8276-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8276-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b8276-217">Vytvořte složku *Pages/instruktory* .</span><span class="sxs-lookup"><span data-stu-id="b8276-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="b8276-218">Spusťte následující příkaz pro generování uživatelského rozhraní stránek instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="b8276-219">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="b8276-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="b8276-220">**V systému Linux nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="b8276-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b8276-221">Chcete-li zjistit, jaké vygenerované stránky vypadá před tím, než je aktualizujete, spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b8276-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="b8276-222">Aktualizovat *stránky/instruktory/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="b8276-223">`OnGetAsync`Metoda přijímá volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b8276-224">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="b8276-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="b8276-225">Kód určuje načítání Eager pro následující navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b8276-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="b8276-226">Všimněte si opakování `Include` a `ThenInclude` metod pro `CourseAssignments` a `Course` .</span><span class="sxs-lookup"><span data-stu-id="b8276-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="b8276-227">Toto opakování je nezbytné pro zadání Eager načítání pro dvě navigační vlastnosti `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="b8276-228">Následující kód se spustí při výběru instruktora ( `id != null` ).</span><span class="sxs-lookup"><span data-stu-id="b8276-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="b8276-229">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b8276-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b8276-230">Vlastnost modelu zobrazení `Courses` je načtena s `Course` entitami z vlastnosti navigace tohoto instruktora `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="b8276-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="b8276-231">`Where`Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="b8276-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="b8276-232">Ale v tomto případě filtr vybere jednu entitu, takže se `Single` zavolá metoda, která kolekci převede na jedinou `Instructor` entitu.</span><span class="sxs-lookup"><span data-stu-id="b8276-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b8276-233">`Instructor`Entita poskytuje přístup k `CourseAssignments` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b8276-234">`CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.</span><span class="sxs-lookup"><span data-stu-id="b8276-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b8276-236">`Single`Metoda se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="b8276-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b8276-237">`Single`Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="b8276-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b8276-238">Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="b8276-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="b8276-239">Následující kód naplní vlastnost modelu zobrazení, `Enrollments` když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="b8276-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b8276-240">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="b8276-240">Update the instructors Index page</span></span>

<span data-ttu-id="b8276-241">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="b8276-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="b8276-242">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="b8276-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b8276-243">Aktualizuje `page` direktivu z `@page` na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="b8276-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b8276-244">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="b8276-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b8276-245">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="b8276-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b8276-246">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze `@page` direktivu, se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="b8276-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="b8276-247">V případě direktivy stránky je `@page "{id:int?}"` Adresa URL:</span><span class="sxs-lookup"><span data-stu-id="b8276-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="b8276-248">Přidá sloupec **Office** , který se zobrazí pouze v případě, že `item.OfficeAssignment.Location` `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="b8276-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b8276-249">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b8276-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b8276-250">Přidá sloupec **kurzů** , ve kterém se zobrazují kurzy, které každý instruktor prodlužuje.</span><span class="sxs-lookup"><span data-stu-id="b8276-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b8276-251">Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="b8276-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="b8276-252">Přidá kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora a kurzu.</span><span class="sxs-lookup"><span data-stu-id="b8276-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="b8276-253">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="b8276-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b8276-254">Přidá nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="b8276-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b8276-255">Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="b8276-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="b8276-256">Přidá tabulku kurzů pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="b8276-257">Přidá tabulku zápisů studentů pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b8276-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="b8276-258">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b8276-259">Pokud `OfficeAssignment` má hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="b8276-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b8276-260">Klikněte na odkaz **Vybrat** pro instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="b8276-261">Zobrazí se změna stylu řádku a kurzy přiřazené k tomuto instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="b8276-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="b8276-262">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="b8276-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="b8276-264">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="b8276-264">Using Single</span></span>

<span data-ttu-id="b8276-265">`Single`Metoda může předat `Where` podmínku namísto volání `Where` metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="b8276-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b8276-266">Použití `Single` s podmínkou WHERE je záležitostí osobní preference.</span><span class="sxs-lookup"><span data-stu-id="b8276-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="b8276-267">Neposkytuje za použití metody žádné výhody `Where` .</span><span class="sxs-lookup"><span data-stu-id="b8276-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b8276-268">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="b8276-268">Explicit loading</span></span>

<span data-ttu-id="b8276-269">Aktuální kód určuje Eager načítání pro `Enrollments` a `Students` :</span><span class="sxs-lookup"><span data-stu-id="b8276-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="b8276-270">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="b8276-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b8276-271">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="b8276-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b8276-272">V této části `OnGetAsync` je aktualizována pro použití explicitního načítání `Enrollments` a `Students` .</span><span class="sxs-lookup"><span data-stu-id="b8276-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b8276-273">Aktualizujte *stránky/instruktory/index. cshtml. cs* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="b8276-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="b8276-274">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="b8276-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b8276-275">Pokud je vybrán kurz, explicitní načítání kódu načte:</span><span class="sxs-lookup"><span data-stu-id="b8276-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="b8276-276">`Enrollment`Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b8276-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b8276-277">`Student`Entity pro každou `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="b8276-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b8276-278">Všimněte si, že předchozí komentáře k kódu byly vycházející `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="b8276-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b8276-279">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b8276-280">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b8276-280">Test the app.</span></span> <span data-ttu-id="b8276-281">V perspektivě uživatele se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="b8276-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b8276-282">Další kroky</span><span class="sxs-lookup"><span data-stu-id="b8276-282">Next steps</span></span>

<span data-ttu-id="b8276-283">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="b8276-284">[Předchozí kurz](xref:data/ef-rp/complex-data-model) 
> [Další kurz](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b8276-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b8276-285">V tomto kurzu se budou číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="b8276-286">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="b8276-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b8276-287">Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="b8276-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="b8276-288">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b8276-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b8276-289">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="b8276-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="b8276-292">Eager, explicitní a opožděné načítání souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="b8276-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="b8276-293">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="b8276-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b8276-294">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b8276-295">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b8276-296">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b8276-297">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="b8276-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b8276-298">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="b8276-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b8276-299">Vystavení více dotazů může být efektivnější než u některých dotazů v EF6, kde existoval jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="b8276-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="b8276-300">Eager načítání je zadáno pomocí `Include` metod a `ThenInclude` .</span><span class="sxs-lookup"><span data-stu-id="b8276-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b8276-302">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="b8276-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b8276-303">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="b8276-303">One query for the main query</span></span> 
  * <span data-ttu-id="b8276-304">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="b8276-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b8276-305">Samostatné dotazy `Load` : data lze načíst v samostatných dotazech a EF Core "" řeší "navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b8276-306">"opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b8276-307">Samostatné dotazy se `Load` podobají explicitnímu načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b8276-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b8276-309">Poznámka: EF Core automaticky opravuje navigační vlastnosti pro všechny další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="b8276-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b8276-310">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="b8276-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b8276-311">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b8276-312">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b8276-313">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="b8276-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b8276-314">Explicitní načítání pomocí samostatných dotazů má za následek více dotazů odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="b8276-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="b8276-315">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="b8276-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b8276-316">`Load`K provedení explicitního načítání použijte metodu.</span><span class="sxs-lookup"><span data-stu-id="b8276-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b8276-317">Zde je příklad:</span><span class="sxs-lookup"><span data-stu-id="b8276-317">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b8276-319">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b8276-320">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b8276-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b8276-321">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b8276-322">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="b8276-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b8276-323">Dotaz se pošle do databáze při prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="b8276-324">`Select`Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="b8276-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="b8276-325">Vytvoří stránku kurzu, která zobrazuje název oddělení.</span><span class="sxs-lookup"><span data-stu-id="b8276-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="b8276-326">Entita kurzu obsahuje navigační vlastnost, která obsahuje `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="b8276-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="b8276-327">`Department`Entita obsahuje oddělení, ke kterému je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="b8276-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="b8276-328">Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů:</span><span class="sxs-lookup"><span data-stu-id="b8276-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="b8276-329">Získá `Name` vlastnost z `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="b8276-330">`Department`Entita pochází z `Course.Department` navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="b8276-332">Generování uživatelského rozhraní modelu kurzu</span><span class="sxs-lookup"><span data-stu-id="b8276-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8276-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8276-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b8276-334">Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b8276-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8276-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8276-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b8276-336">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b8276-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="b8276-337">Předchozí příkaz vygeneruje `Course` model.</span><span class="sxs-lookup"><span data-stu-id="b8276-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="b8276-338">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b8276-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="b8276-339">Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="b8276-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b8276-340">Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b8276-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b8276-341">`Include`Metoda určuje Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="b8276-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="b8276-342">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="b8276-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b8276-343">Sloupec oddělení zobrazí `DepartmentID` , což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="b8276-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="b8276-344">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="b8276-345">Předchozí kód přidá `AsNoTracking` .</span><span class="sxs-lookup"><span data-stu-id="b8276-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="b8276-346">`AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="b8276-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b8276-347">Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="b8276-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b8276-348">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="b8276-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="b8276-349">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="b8276-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b8276-350">Změnili jste záhlaví z indexu na kurzy.</span><span class="sxs-lookup"><span data-stu-id="b8276-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="b8276-351">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b8276-352">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="b8276-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b8276-353">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="b8276-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b8276-354">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="b8276-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b8276-355">Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do `Department` navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b8276-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b8276-356">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="b8276-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="b8276-358">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="b8276-358">Loading related data with Select</span></span>

<span data-ttu-id="b8276-359">`OnGetAsync`Metoda načte související data pomocí `Include` metody:</span><span class="sxs-lookup"><span data-stu-id="b8276-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b8276-360">`Select`Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="b8276-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="b8276-361">Pro jednotlivé položky, například `Department.Name` používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="b8276-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b8276-362">U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.</span><span class="sxs-lookup"><span data-stu-id="b8276-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b8276-363">Následující kód načte související data `Select` metodou:</span><span class="sxs-lookup"><span data-stu-id="b8276-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b8276-364">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b8276-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b8276-365">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="b8276-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="b8276-366">Vytvoření stránky instruktory, která zobrazuje kurzy a registrace</span><span class="sxs-lookup"><span data-stu-id="b8276-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="b8276-367">V této části se vytvoří stránka instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b8276-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="b8276-368">![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="b8276-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="b8276-369">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="b8276-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b8276-370">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="b8276-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b8276-371">`Instructor`Entity a `OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota.</span><span class="sxs-lookup"><span data-stu-id="b8276-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b8276-372">Pro entity se používá Eager načítání `OfficeAssignment` .</span><span class="sxs-lookup"><span data-stu-id="b8276-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b8276-373">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b8276-374">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="b8276-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b8276-375">Když uživatel vybere instruktora (Harui na předchozím obrázku), `Course` zobrazí se související entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="b8276-376">`Instructor`Entity a `Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="b8276-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b8276-377">Eager načítání se používá pro `Course` entity a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b8276-378">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b8276-379">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="b8276-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b8276-380">Když uživatel vybere kurz (chemie na předchozím obrázku), zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b8276-381">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="b8276-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b8276-382">`Course`Entity a `Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="b8276-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="b8276-383">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="b8276-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="b8276-384">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="b8276-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b8276-385">Vytvoří se model zobrazení, který obsahuje tři entity reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="b8276-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="b8276-386">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="b8276-387">Generování uživatelského rozhraní modelu instruktor</span><span class="sxs-lookup"><span data-stu-id="b8276-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b8276-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8276-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b8276-389">Postupujte podle pokynů v [části generátor a model student](xref:data/ef-rp/intro#scaffold-the-student-model) a použijte `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b8276-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b8276-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b8276-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="b8276-391">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b8276-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="b8276-392">Předchozí příkaz vygeneruje `Instructor` model.</span><span class="sxs-lookup"><span data-stu-id="b8276-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="b8276-393">Spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b8276-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="b8276-394">*Stránky/instruktořis/index. cshtml. cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="b8276-395">`OnGetAsync`Metoda přijímá volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b8276-396">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="b8276-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b8276-397">Dotaz obsahuje dvě:</span><span class="sxs-lookup"><span data-stu-id="b8276-397">The query has two includes:</span></span>

* <span data-ttu-id="b8276-398">`OfficeAssignment`: Zobrazí se v [zobrazení instruktory](#IP).</span><span class="sxs-lookup"><span data-stu-id="b8276-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="b8276-399">`CourseAssignments`: Přináší výuku kurzů.</span><span class="sxs-lookup"><span data-stu-id="b8276-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b8276-400">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="b8276-400">Update the instructors Index page</span></span>

<span data-ttu-id="b8276-401">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b8276-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="b8276-402">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="b8276-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="b8276-403">Aktualizuje `page` direktivu z `@page` na `@page "{id:int?}"` .</span><span class="sxs-lookup"><span data-stu-id="b8276-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b8276-404">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="b8276-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b8276-405">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="b8276-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b8276-406">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze `@page` direktivu, se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="b8276-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="b8276-407">Pokud je direktiva stránky `@page "{id:int?}"` , předchozí adresa URL:</span><span class="sxs-lookup"><span data-stu-id="b8276-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="b8276-408">Nadpis stránky jsou **instruktory**.</span><span class="sxs-lookup"><span data-stu-id="b8276-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="b8276-409">Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v případě, že `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="b8276-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b8276-410">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b8276-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b8276-411">Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem.</span><span class="sxs-lookup"><span data-stu-id="b8276-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b8276-412">Další informace o této syntaxi Razor naleznete v tématu [explicitní přechod mezi řádky](xref:mvc/views/razor#explicit-line-transition) .</span><span class="sxs-lookup"><span data-stu-id="b8276-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="b8276-413">Přidaný kód, který dynamicky přidá `class="success"` do `tr` prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="b8276-414">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="b8276-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b8276-415">Byl přidán nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="b8276-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b8276-416">Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="b8276-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="b8276-417">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b8276-418">Pokud má OfficeAssignment hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="b8276-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="b8276-419">Klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="b8276-419">Click on the **Select** link.</span></span> <span data-ttu-id="b8276-420">Styl řádku se změní.</span><span class="sxs-lookup"><span data-stu-id="b8276-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="b8276-421">Přidat výukové kurzy podle vybraného instruktora</span><span class="sxs-lookup"><span data-stu-id="b8276-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="b8276-422">Aktualizujte `OnGetAsync` metodu na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="b8276-423">Přidávání`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="b8276-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="b8276-424">Projděte si aktualizovaný dotaz:</span><span class="sxs-lookup"><span data-stu-id="b8276-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b8276-425">Předchozí dotaz přidá `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="b8276-426">Následující kód se spustí při výběru instruktora ( `id != null` ).</span><span class="sxs-lookup"><span data-stu-id="b8276-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="b8276-427">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b8276-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b8276-428">Vlastnost modelu zobrazení `Courses` je načtena s `Course` entitami z vlastnosti navigace tohoto instruktora `CourseAssignments` .</span><span class="sxs-lookup"><span data-stu-id="b8276-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="b8276-429">`Where`Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="b8276-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="b8276-430">V předchozí `Where` metodě `Instructor` se vrátí jenom jedna entita.</span><span class="sxs-lookup"><span data-stu-id="b8276-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="b8276-431">`Single`Metoda převede kolekci na jednu `Instructor` entitu.</span><span class="sxs-lookup"><span data-stu-id="b8276-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b8276-432">`Instructor`Entita poskytuje přístup k `CourseAssignments` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b8276-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b8276-433">`CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.</span><span class="sxs-lookup"><span data-stu-id="b8276-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b8276-435">`Single`Metoda se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="b8276-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b8276-436">`Single`Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="b8276-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b8276-437">Alternativa je `SingleOrDefault` , která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="b8276-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="b8276-438">Použití `SingleOrDefault` v prázdné kolekci:</span><span class="sxs-lookup"><span data-stu-id="b8276-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="b8276-439">Výsledkem je výjimka (při pokusu o nalezení `Courses` vlastnosti v odkazu s hodnotou null).</span><span class="sxs-lookup"><span data-stu-id="b8276-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="b8276-440">Zpráva o výjimce by byla zřetelně označovat příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="b8276-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="b8276-441">Následující kód naplní vlastnost modelu zobrazení, `Enrollments` když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="b8276-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="b8276-442">Na konec stránky *stránky/instruktory/index. cshtml* přidejte následující kód Razor :</span><span class="sxs-lookup"><span data-stu-id="b8276-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="b8276-443">Předchozí kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="b8276-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="b8276-444">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b8276-444">Test the app.</span></span> <span data-ttu-id="b8276-445">Na stránce instruktoři klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="b8276-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="b8276-446">Zobrazit data studenta</span><span class="sxs-lookup"><span data-stu-id="b8276-446">Show student data</span></span>

<span data-ttu-id="b8276-447">V této části se aplikace aktualizuje, aby zobrazovala údaje o studentech pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b8276-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="b8276-448">Aktualizujte dotaz v `OnGetAsync` metodě na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b8276-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b8276-449">Aktualizovat *stránky/instruktory/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="b8276-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="b8276-450">Na konec souboru přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="b8276-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="b8276-451">Předchozí kód zobrazuje seznam studentů, kteří jsou zaregistrovaní ve vybraném kurzu.</span><span class="sxs-lookup"><span data-stu-id="b8276-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="b8276-452">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="b8276-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="b8276-453">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="b8276-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="b8276-455">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="b8276-455">Using Single</span></span>

<span data-ttu-id="b8276-456">`Single`Metoda může předat `Where` podmínku namísto volání `Where` metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="b8276-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b8276-457">Předchozí `Single` přístup neposkytuje oproti použití žádné výhody `Where` .</span><span class="sxs-lookup"><span data-stu-id="b8276-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="b8276-458">Někteří vývojáři upřednostňují `Single` styl přístupu.</span><span class="sxs-lookup"><span data-stu-id="b8276-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b8276-459">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="b8276-459">Explicit loading</span></span>

<span data-ttu-id="b8276-460">Aktuální kód určuje Eager načítání pro `Enrollments` a `Students` :</span><span class="sxs-lookup"><span data-stu-id="b8276-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b8276-461">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="b8276-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b8276-462">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="b8276-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b8276-463">V této části `OnGetAsync` je aktualizována pro použití explicitního načítání `Enrollments` a `Students` .</span><span class="sxs-lookup"><span data-stu-id="b8276-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b8276-464">Aktualizujte `OnGetAsync` pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b8276-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="b8276-465">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="b8276-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b8276-466">Pokud je vybrán kurz, zvýrazněný kód načte:</span><span class="sxs-lookup"><span data-stu-id="b8276-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="b8276-467">`Enrollment`Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b8276-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b8276-468">`Student`Entity pro každou `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="b8276-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b8276-469">Všimněte si předcházejících komentářů k kódu `.AsNoTracking()` .</span><span class="sxs-lookup"><span data-stu-id="b8276-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b8276-470">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="b8276-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b8276-471">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b8276-471">Test the app.</span></span> <span data-ttu-id="b8276-472">V perspektivě uživatelů se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="b8276-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="b8276-473">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="b8276-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8276-474">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b8276-474">Additional resources</span></span>

* [<span data-ttu-id="b8276-475">Verze tohoto kurzu pro YouTube (part1)</span><span class="sxs-lookup"><span data-stu-id="b8276-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="b8276-476">Verze tohoto kurzu pro YouTube (Část2)</span><span class="sxs-lookup"><span data-stu-id="b8276-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="b8276-477">[Předchozí](xref:data/ef-rp/complex-data-model) 
> [Další](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b8276-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
