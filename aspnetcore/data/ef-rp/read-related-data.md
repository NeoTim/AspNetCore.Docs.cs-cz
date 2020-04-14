---
title: Razor Stránky s EF core v ASP.NET Core - Číst související data - 6 z 8
author: rick-anderson
description: V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která rozhraní Entity Framework načte do navigačních vlastností.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 0835dbf6b5434e4224d9f56ea3335df4b5d9e119
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277324"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="5fe9f-103">Razor Stránky s EF core v ASP.NET Core - Číst související data - 6 z 8</span><span class="sxs-lookup"><span data-stu-id="5fe9f-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="5fe9f-104">[Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), a Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5fe9f-105">Tento kurz ukazuje, jak číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="5fe9f-106">Související data jsou data, která EF Core načte do navigačních vlastností.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="5fe9f-107">Následující ilustrace znázornit dokončené stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka Index kurzů](read-related-data/_static/courses-index30.png)

![Stránka Index instruktorů](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="5fe9f-110">Dychtivé, explicitní a opožděné načítání</span><span class="sxs-lookup"><span data-stu-id="5fe9f-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="5fe9f-111">Existuje několik způsobů, jak EF Core můžete načíst související data do navigačnívlastnosti entity:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="5fe9f-112">[Dychtivé načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="5fe9f-113">Eager loading je, když dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="5fe9f-114">Při čtení entity se načtou její související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="5fe9f-115">To obvykle vede k dotazu na jedno spojení, který načte všechna data, která je potřeba.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="5fe9f-116">EF Core vydá více dotazů pro některé typy dychtivé načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="5fe9f-117">Vydávání více dotazů může být efektivnější než obří jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="5fe9f-118">Eager loading je `Include` zadán `ThenInclude` s a metody.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad dychtivénačítání](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="5fe9f-120">Eager načítání odešle více dotazů, pokud je zahrnuta navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="5fe9f-121">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="5fe9f-121">One query for the main query</span></span> 
  * <span data-ttu-id="5fe9f-122">Jeden dotaz pro každou kolekci "okraj" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="5fe9f-123">Samostatné dotazy `Load`s : Data lze načíst v samostatných dotazech a EF Core "opravuje" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="5fe9f-124">"Opravy" znamená, že EF Core automaticky naplní navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="5fe9f-125">Samostatné dotazy `Load` s je spíše explicitní načítání než eager načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="5fe9f-127">Poznámka: EF Core automaticky opravuje navigační vlastnosti na všechny ostatní entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="5fe9f-128">I v případě, že data pro navigační vlastnost *není* explicitně zahrnuta, vlastnost může být stále naplněna, pokud byly dříve načteny některé nebo všechny související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="5fe9f-129">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="5fe9f-130">Při prvním čtení entity se nenačtou související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5fe9f-131">Kód musí být zapsán, aby bylo možné načíst související data, když je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="5fe9f-132">Explicitní načítání se samostatnými dotazy má za následek více dotazů odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="5fe9f-133">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="5fe9f-134">Použijte `Load` metodu k explicitnímu načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="5fe9f-135">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-135">For example:</span></span>

  ![Příklad explicitního načítání](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="5fe9f-137">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5fe9f-138">[Opožděné načtení bylo přidáno do EF Core ve verzi 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5fe9f-139">Při prvním čtení entity se nenačtou související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5fe9f-140">Při prvním přístupu k navigační vlastnosti jsou automaticky načtena data požadovaná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="5fe9f-141">Dotaz je odeslán do databáze pokaždé, když navigační vlastnost je přístupná poprvé.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="5fe9f-142">Vytvořit stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="5fe9f-142">Create Course pages</span></span>

<span data-ttu-id="5fe9f-143">Entita `Course` obsahuje navigační vlastnost, `Department` která obsahuje související entitu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="5fe9f-145">Zobrazení názvu přiřazeného oddělení kurzu:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="5fe9f-146">Načtěte `Department` související `Course.Department` entitu do vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="5fe9f-147">Získejte název `Department` z `Name` vlastnosti entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="5fe9f-148">Lešení Stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="5fe9f-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5fe9f-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5fe9f-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5fe9f-150">Postupujte podle pokynů na [stránkách Student lešení](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="5fe9f-151">Vytvořte složku *Stránky/kurzy.*</span><span class="sxs-lookup"><span data-stu-id="5fe9f-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="5fe9f-152">Slouží `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="5fe9f-153">Místo vytvoření nové třídy kontextu použijte existující třídu kontextu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5fe9f-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5fe9f-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5fe9f-155">Vytvořte složku *Stránky/kurzy.*</span><span class="sxs-lookup"><span data-stu-id="5fe9f-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="5fe9f-156">Spusťte následující příkaz pro zaslístvo stránek kurzu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="5fe9f-157">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="5fe9f-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="5fe9f-158">**Na Linuxu nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="5fe9f-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="5fe9f-159">Otevřete *stránky/kurzy/index.cshtml.cs* a zkontrolujte metodu. `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="5fe9f-160">Modul lešení zadal dychtivé `Department` zatížení pro vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="5fe9f-161">Metoda `Include` určuje eager načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="5fe9f-162">Spusťte aplikaci a vyberte odkaz **Kurzy.**</span><span class="sxs-lookup"><span data-stu-id="5fe9f-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="5fe9f-163">Sloupec oddělení zobrazuje `DepartmentID`, což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="5fe9f-164">Zobrazení názvu oddělení</span><span class="sxs-lookup"><span data-stu-id="5fe9f-164">Display the department name</span></span>

<span data-ttu-id="5fe9f-165">Aktualizujte stránky/kurzy/index.cshtml.cs pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="5fe9f-166">Předchozí kód změní `Course` vlastnost `Courses` a `AsNoTracking`přidá .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="5fe9f-167">`AsNoTracking`zlepšuje výkon, protože vrácené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="5fe9f-168">Entity není nutné sledovat, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="5fe9f-169">Aktualizujte *stránky/kurzy/index.cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="5fe9f-170">V kódu skládaným lešení byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="5fe9f-171">Název `Course` vlastnosti `Courses`byl změněn na .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="5fe9f-172">Byl přidán sloupec **Číslo,** který zobrazuje hodnotu vlastnosti. `CourseID`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="5fe9f-173">Ve výchozím nastavení nejsou primární klíče skládané, protože obvykle nemají pro koncové uživatele smysl.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="5fe9f-174">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="5fe9f-175">Sloupec **Oddělení** se změnil tak, aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="5fe9f-176">Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do vlastnosti `Department` navigace:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="5fe9f-177">Spusťte aplikaci a vyberte kartu **Kurzy,** abyste viděli seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka Index kurzů](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="5fe9f-179">Načítání souvisejících dat pomocí funkce Vybrat</span><span class="sxs-lookup"><span data-stu-id="5fe9f-179">Loading related data with Select</span></span>

<span data-ttu-id="5fe9f-180">Metoda `OnGetAsync` načte související `Include` data s metodou.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="5fe9f-181">Metoda `Select` je alternativou, která načte pouze související data potřebná.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="5fe9f-182">Pro jednotlivé položky, jako `Department.Name` je sql inner join.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="5fe9f-183">Pro kolekce používá jiný přístup k databázi, ale stejně tak operátor na `Include` kolekce.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="5fe9f-184">Následující kód načte související `Select` data s metodou:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="5fe9f-185">Předchozí kód nevrátí žádné typy entit, proto se neprovádí žádné sledování.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="5fe9f-186">Další informace o ef sledování, naleznete [v tématu sledování vs bez sledování dotazy](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="5fe9f-187">V: `CourseViewModel`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="5fe9f-188">Úplný příklad najdete v [tématu IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="5fe9f-189">Vytvořit stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="5fe9f-189">Create Instructor pages</span></span>

<span data-ttu-id="5fe9f-190">Tato část scaffolds instruktor stránky a přidá související kurzy a zápisy do index instruktorů stránky.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="5fe9f-191">![Stránka Index instruktorů](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="5fe9f-192">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="5fe9f-193">Seznam instruktorů zobrazuje související data `OfficeAssignment` z entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="5fe9f-194">Entity `Instructor` `OfficeAssignment` a jsou ve vztahu 1:0 nebo 1.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="5fe9f-195">Eager načítání se `OfficeAssignment` používá pro entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="5fe9f-196">Eager načítání je obvykle efektivnější, když je třeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="5fe9f-197">V tomto případě jsou zobrazeny kancelářské úkoly pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="5fe9f-198">Když uživatel vybere instruktora, `Course` zobrazí se související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="5fe9f-199">A `Instructor` `Course` entity jsou ve vztahu N:N.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="5fe9f-200">Eager načítání se `Course` používá pro entity `Department` a jejich související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="5fe9f-201">V takovém případě mohou být samostatné dotazy efektivnější, protože jsou potřeba pouze kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="5fe9f-202">Tento příklad ukazuje, jak použít dychtivé načítání pro navigační vlastnosti v entitách, které jsou v navigačních vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="5fe9f-203">Když uživatel vybere kurz, zobrazí se `Enrollments` související data z entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="5fe9f-204">Na předchozím obrázku se zobrazí jméno a hodnocení studenta.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="5fe9f-205">A `Course` `Enrollment` entity jsou ve vztahu 1:N.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="5fe9f-206">Vytvoření modelu zobrazení</span><span class="sxs-lookup"><span data-stu-id="5fe9f-206">Create a view model</span></span>

<span data-ttu-id="5fe9f-207">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="5fe9f-208">Je potřeba model zobrazení, který obsahuje tři vlastnosti představující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="5fe9f-209">Vytvořte *schoolviewmodels/instructorindexdata.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="5fe9f-210">Stránky instruktora lešení</span><span class="sxs-lookup"><span data-stu-id="5fe9f-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5fe9f-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5fe9f-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5fe9f-212">Postupujte podle pokynů v [Lešení student stránky](xref:data/ef-rp/intro#scaffold-student-pages) s následujícími výjimkami:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="5fe9f-213">Vytvořte složku *Stránky/instruktoři.*</span><span class="sxs-lookup"><span data-stu-id="5fe9f-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="5fe9f-214">Slouží `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="5fe9f-215">Místo vytvoření nové třídy kontextu použijte existující třídu kontextu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5fe9f-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5fe9f-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5fe9f-217">Vytvořte složku *Stránky/instruktoři.*</span><span class="sxs-lookup"><span data-stu-id="5fe9f-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="5fe9f-218">Spusťte následující příkaz pro zakládání uživatelského lístí stránek instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="5fe9f-219">**Ve Windows:**</span><span class="sxs-lookup"><span data-stu-id="5fe9f-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="5fe9f-220">**Na Linuxu nebo macOS:**</span><span class="sxs-lookup"><span data-stu-id="5fe9f-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="5fe9f-221">Chcete-li zjistit, jak vypadá stránka s lešením, než ji aktualizujete, spusťte aplikaci a přejděte na stránku Instruktoři.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="5fe9f-222">Aktualizujte *stránky/instruktoři/Index.cshtml.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="5fe9f-223">Metoda `OnGetAsync` přijímá volitelná data trasy pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="5fe9f-224">Zkontrolujte dotaz v souboru *Pages/Instructors/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="5fe9f-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="5fe9f-225">Kód určuje nedočkavé načítání pro následující navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="5fe9f-226">Všimněte si `Include` opakování `ThenInclude` a `CourseAssignments` `Course`metody pro a .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="5fe9f-227">Toto opakování je nezbytné určit dychtivé načítání `Course` pro dvě navigační vlastnosti entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="5fe9f-228">Následující kód se spustí, když`id != null`je vybrán instruktor ( ).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="5fe9f-229">Vybraný instruktor je načten ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="5fe9f-230">`Courses` Vlastnost modelu zobrazení je načtena `Course` s entitami `CourseAssignments` z navigační vlastnosti tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="5fe9f-231">Metoda `Where` vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="5fe9f-232">V tomto případě však filtr vybere jednu entitu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="5fe9f-233">proto `Single` je metoda volána k převodu kolekce na jednu `Instructor` entitu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="5fe9f-234">Entita `Instructor` poskytuje `CourseAssignments` přístup k vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="5fe9f-235">`CourseAssignments`poskytuje přístup k `Course` propojeným subjektům.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5fe9f-237">Metoda `Single` se používá v kolekci, pokud kolekce má pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="5fe9f-238">Metoda `Single` vyvolá výjimku, pokud je kolekce prázdná nebo pokud existuje více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="5fe9f-239">Alternativou `SingleOrDefault`je , která vrátí výchozí hodnotu (null v tomto případě), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="5fe9f-240">Následující kód naplní `Enrollments` vlastnost modelu zobrazení, když je vybrán kurz:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="5fe9f-241">Aktualizace stránky Rejstříku instruktorů</span><span class="sxs-lookup"><span data-stu-id="5fe9f-241">Update the instructors Index page</span></span>

<span data-ttu-id="5fe9f-242">Aktualizujte *stránky/instruktoři/Index.cshtml* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="5fe9f-243">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="5fe9f-244">Aktualizuje `page` direktivu z `@page` na . `@page "{id:int?}"`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="5fe9f-245">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="5fe9f-246">Šablona trasy změní řetězce dotazů celé číslo v adrese URL pro směrování dat.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="5fe9f-247">Například kliknutím na odkaz **Vybrat** pro instruktora s pouze `@page` direktivou se zobrazí adresa URL, jako je tato:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="5fe9f-248">Pokud je `@page "{id:int?}"`direktiva stránky , adresa URL je:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="5fe9f-249">Přidá sloupec **Office,** který se zobrazí `item.OfficeAssignment.Location` pouze v případě, `item.OfficeAssignment` že nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="5fe9f-250">Vzhledem k tomu, že se jedná o vztah 1:Nula nebo jeden, nemusí existovat související entita OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="5fe9f-251">Přidá sloupec **Kurzy,** který zobrazuje kurzy vyučované jednotlivými instruktory.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="5fe9f-252">Další informace o této syntaxi holicího strojku naleznete [v tématu Explicitní přechod řádku.](xref:mvc/views/razor#explicit-line-transition)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="5fe9f-253">Přidá kód, který `class="success"` dynamicky přidá k `tr` prvku vybraného instruktora a kurzu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="5fe9f-254">Tím nastavíte barvu pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="5fe9f-255">Přidá nový hypertextový odkaz s názvem **Vybrat**.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="5fe9f-256">Tento odkaz odešle ID vybraného `Index` instruktora metodě a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="5fe9f-257">Přidá tabulku kurzů pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="5fe9f-258">Přidá tabulku zápisů studentů pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="5fe9f-259">Spusťte aplikaci a vyberte kartu **Instruktoři.** Na stránce `Location` se zobrazí (kancelář) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="5fe9f-260">Pokud `OfficeAssignment` je null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="5fe9f-261">Klikněte na odkaz **Vybrat** pro instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="5fe9f-262">Zobrazí se změny stylu řádku a kurzy přiřazené tomuto instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="5fe9f-263">Výběrem kurzu zobrazíte seznam zapsaných studentů a jejich známky.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-263">Select a course to see the list of enrolled students and their grades.</span></span>

![Instruktoři Index stránky instruktor a kurz vybrán](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="5fe9f-265">Použití jednoduchého</span><span class="sxs-lookup"><span data-stu-id="5fe9f-265">Using Single</span></span>

<span data-ttu-id="5fe9f-266">Metoda `Single` může předat `Where` v podmínce `Where` namísto volání metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="5fe9f-267">Použití `Single` s Podmínka Kde je záležitostí osobních preferencí.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="5fe9f-268">Poskytuje žádné výhody oproti `Where` použití metody.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="5fe9f-269">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="5fe9f-269">Explicit loading</span></span>

<span data-ttu-id="5fe9f-270">Aktuální kód určuje nedočkavé načítání pro `Enrollments` a `Students`:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="5fe9f-271">Předpokládejme, že uživatelé zřídka chtějí zobrazit zápisy v kurzu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="5fe9f-272">V takovém případě optimalizace by bylo načíst pouze data zápisu, pokud je požadováno.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="5fe9f-273">V této části `OnGetAsync` je aktualizován použít `Enrollments` explicitní `Students`načítání a .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="5fe9f-274">Aktualizujte *stránky/instruktoři/Index.cshtml.cs* pomocí následujícího kódu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="5fe9f-275">Předchozí kód klesne *ThenInclude* volání metody pro zápis a data studenta.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="5fe9f-276">Pokud je vybrán kurz, načte se explicitní načítací kód:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="5fe9f-277">Entity `Enrollment` pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="5fe9f-278">Entity `Student` pro každý `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="5fe9f-279">Všimněte si, že `.AsNoTracking()`předchozí kód komentáře ven .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="5fe9f-280">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="5fe9f-281">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-281">Test the app.</span></span> <span data-ttu-id="5fe9f-282">Z pohledu uživatele se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5fe9f-283">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5fe9f-283">Next steps</span></span>

<span data-ttu-id="5fe9f-284">Další kurz ukazuje, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="5fe9f-285">[Předchozí kurz](xref:data/ef-rp/complex-data-model)
>[Další kurz](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5fe9f-286">V tomto kurzu se číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="5fe9f-287">Související data jsou data, která EF Core načte do navigačních vlastností.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="5fe9f-288">Pokud narazíte na problémy, které nemůžete vyřešit, [stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="5fe9f-289">[Stáhnout pokyny](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="5fe9f-290">Následující ilustrace znázornit dokončené stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="5fe9f-293">Dychtivé, explicitní a opožděné načítání souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="5fe9f-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="5fe9f-294">Existuje několik způsobů, jak EF Core můžete načíst související data do navigačnívlastnosti entity:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="5fe9f-295">[Dychtivé načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="5fe9f-296">Eager loading je, když dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="5fe9f-297">Při čtení entity se načtou její související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="5fe9f-298">To obvykle vede k dotazu na jedno spojení, který načte všechna data, která je potřeba.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="5fe9f-299">EF Core vydá více dotazů pro některé typy dychtivé načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="5fe9f-300">Vydávání více dotazů může být efektivnější, než tomu bylo v případě některých dotazů v EF6, kde byl jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="5fe9f-301">Eager loading je `Include` zadán `ThenInclude` s a metody.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad dychtivénačítání](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="5fe9f-303">Eager načítání odešle více dotazů, pokud je zahrnuta navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="5fe9f-304">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="5fe9f-304">One query for the main query</span></span> 
  * <span data-ttu-id="5fe9f-305">Jeden dotaz pro každou kolekci "okraj" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="5fe9f-306">Samostatné dotazy `Load`s : Data lze načíst v samostatných dotazech a EF Core "opravuje" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="5fe9f-307">"Opravy" znamená, že EF Core automaticky naplní navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="5fe9f-308">Samostatné dotazy `Load` s je spíše explicitní načítání než eager načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="5fe9f-310">Poznámka: EF Core automaticky opravuje navigační vlastnosti na všechny ostatní entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="5fe9f-311">I v případě, že data pro navigační vlastnost *není* explicitně zahrnuta, vlastnost může být stále naplněna, pokud byly dříve načteny některé nebo všechny související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="5fe9f-312">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="5fe9f-313">Při prvním čtení entity se nenačtou související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5fe9f-314">Kód musí být zapsán, aby bylo možné načíst související data, když je to potřeba.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="5fe9f-315">Explicitní načítání se samostatnými dotazy má za následek více dotazů odeslaných do db.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="5fe9f-316">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="5fe9f-317">Použijte `Load` metodu k explicitnímu načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="5fe9f-318">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-318">For example:</span></span>

  ![Příklad explicitního načítání](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="5fe9f-320">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5fe9f-321">[Opožděné načtení bylo přidáno do EF Core ve verzi 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="5fe9f-322">Při prvním čtení entity se nenačtou související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="5fe9f-323">Při prvním přístupu k navigační vlastnosti jsou automaticky načtena data požadovaná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="5fe9f-324">Dotaz je odeslán do DB pokaždé, když navigační vlastnost je přístupná poprvé.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="5fe9f-325">Operátor `Select` načte pouze související potřebná data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="5fe9f-326">Vytvoření stránky kurzu, která zobrazuje název oddělení</span><span class="sxs-lookup"><span data-stu-id="5fe9f-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="5fe9f-327">Entita Course obsahuje navigační `Department` vlastnost, která obsahuje entitu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="5fe9f-328">Entita `Department` obsahuje oddělení, ke kterému je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="5fe9f-329">Zobrazení názvu přiřazeného oddělení v seznamu kurzů:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="5fe9f-330">Získejte `Name` vlastnost `Department` z entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="5fe9f-331">Entita `Department` pochází `Course.Department` z navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="5fe9f-333">Lešení modelu kurzu</span><span class="sxs-lookup"><span data-stu-id="5fe9f-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5fe9f-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5fe9f-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="5fe9f-335">Postupujte podle pokynů v [lešení model studenta](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Course` pro model třídy.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5fe9f-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5fe9f-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="5fe9f-337">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="5fe9f-338">Předchozí příkaz uchylovací `Course` příkaz y model.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="5fe9f-339">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="5fe9f-340">Otevřete *stránky/kurzy/index.cshtml.cs* a zkontrolujte metodu. `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="5fe9f-341">Modul lešení zadal dychtivé `Department` zatížení pro vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="5fe9f-342">Metoda `Include` určuje eager načítání.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="5fe9f-343">Spusťte aplikaci a vyberte odkaz **Kurzy.**</span><span class="sxs-lookup"><span data-stu-id="5fe9f-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="5fe9f-344">Sloupec oddělení zobrazuje `DepartmentID`, což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="5fe9f-345">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="5fe9f-346">Předchozí kód přidá `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="5fe9f-347">`AsNoTracking`zlepšuje výkon, protože vrácené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="5fe9f-348">Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="5fe9f-349">Aktualizujte *stránky/kurzy/index.cshtml* pomocí následujících zvýrazněných značek:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="5fe9f-350">V kódu skládaným lešení byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="5fe9f-351">Nadpis byl změněn z rejstříku na Kurzy.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="5fe9f-352">Byl přidán sloupec **Číslo,** který zobrazuje hodnotu vlastnosti. `CourseID`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="5fe9f-353">Ve výchozím nastavení nejsou primární klíče skládané, protože obvykle nemají pro koncové uživatele smysl.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="5fe9f-354">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="5fe9f-355">Sloupec **Oddělení** se změnil tak, aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="5fe9f-356">Kód zobrazí `Name` vlastnost `Department` entity, která je načtena do vlastnosti `Department` navigace:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="5fe9f-357">Spusťte aplikaci a vyberte kartu **Kurzy,** abyste viděli seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka Index kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="5fe9f-359">Načítání souvisejících dat pomocí funkce Vybrat</span><span class="sxs-lookup"><span data-stu-id="5fe9f-359">Loading related data with Select</span></span>

<span data-ttu-id="5fe9f-360">Metoda `OnGetAsync` načte související `Include` data s metodou:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="5fe9f-361">Operátor `Select` načte pouze související potřebná data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="5fe9f-362">Pro jednotlivé položky, jako `Department.Name` je sql inner join.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="5fe9f-363">Pro kolekce používá jiný přístup k databázi, ale stejně tak operátor na `Include` kolekce.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="5fe9f-364">Následující kód načte související `Select` data s metodou:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="5fe9f-365">V: `CourseViewModel`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="5fe9f-366">Úplný příklad najdete v [tématu IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="5fe9f-367">Vytvoření stránky instruktoři, která zobrazuje kurzy a zápisy</span><span class="sxs-lookup"><span data-stu-id="5fe9f-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="5fe9f-368">V této části je vytvořena stránka Instruktoři.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="5fe9f-369">![Stránka Index instruktorů](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="5fe9f-370">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="5fe9f-371">Seznam instruktorů zobrazuje související data `OfficeAssignment` z entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="5fe9f-372">Entity `Instructor` `OfficeAssignment` a jsou ve vztahu 1:0 nebo 1.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="5fe9f-373">Eager načítání se `OfficeAssignment` používá pro entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="5fe9f-374">Eager načítání je obvykle efektivnější, když je třeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="5fe9f-375">V tomto případě jsou zobrazeny kancelářské úkoly pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="5fe9f-376">Když uživatel vybere instruktora (Harui v předchozím `Course` obrázku), zobrazí se související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="5fe9f-377">A `Instructor` `Course` entity jsou ve vztahu N:N.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="5fe9f-378">Eager načítání se `Course` používá pro entity `Department` a jejich související entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="5fe9f-379">V takovém případě mohou být samostatné dotazy efektivnější, protože jsou potřeba pouze kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="5fe9f-380">Tento příklad ukazuje, jak použít dychtivé načítání pro navigační vlastnosti v entitách, které jsou v navigačních vlastnostech.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="5fe9f-381">Když uživatel vybere kurz (Chemie v předchozím obrázku), `Enrollments` zobrazí se související data z entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="5fe9f-382">Na předchozím obrázku se zobrazí jméno a hodnocení studenta.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="5fe9f-383">A `Course` `Enrollment` entity jsou ve vztahu 1:N.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="5fe9f-384">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="5fe9f-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="5fe9f-385">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="5fe9f-386">Je vytvořen model zobrazení, který zahrnuje tři entity představující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="5fe9f-387">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="5fe9f-388">Lešení instruktormodel</span><span class="sxs-lookup"><span data-stu-id="5fe9f-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5fe9f-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5fe9f-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="5fe9f-390">Postupujte podle pokynů v [lešení model studenta](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Instructor` pro model třídy.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5fe9f-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5fe9f-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="5fe9f-392">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="5fe9f-393">Předchozí příkaz uchylovací `Instructor` příkaz y model.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="5fe9f-394">Spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="5fe9f-395">Nahraďte *stránky/instruktoři/index.cshtml.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="5fe9f-396">Metoda `OnGetAsync` přijímá volitelná data trasy pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="5fe9f-397">Zkontrolujte dotaz v souboru *Pages/Instructors/Index.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="5fe9f-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="5fe9f-398">Dotaz má dvě zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-398">The query has two includes:</span></span>

* <span data-ttu-id="5fe9f-399">`OfficeAssignment`: Zobrazeno v [zobrazení instruktorů](#IP).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="5fe9f-400">`CourseAssignments`: Což přináší vyučované kurzy.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="5fe9f-401">Aktualizace stránky Rejstříku instruktorů</span><span class="sxs-lookup"><span data-stu-id="5fe9f-401">Update the instructors Index page</span></span>

<span data-ttu-id="5fe9f-402">Aktualizujte *stránky/instruktoři/Index.cshtml* pomocí následujících značek:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="5fe9f-403">Předchozí značky provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="5fe9f-404">Aktualizuje `page` direktivu z `@page` na . `@page "{id:int?}"`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="5fe9f-405">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="5fe9f-406">Šablona trasy změní řetězce dotazů celé číslo v adrese URL pro směrování dat.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="5fe9f-407">Například kliknutím na odkaz **Vybrat** pro instruktora s pouze `@page` direktivou se zobrazí adresa URL, jako je tato:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="5fe9f-408">Pokud je `@page "{id:int?}"`direktiva stránky , předchozí adresa URL je:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="5fe9f-409">Název stránky je **Instruktoři**.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="5fe9f-410">Přidán sloupec **Office,** který se zobrazí `item.OfficeAssignment.Location` pouze v případě, `item.OfficeAssignment` že nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="5fe9f-411">Vzhledem k tomu, že se jedná o vztah 1:Nula nebo jeden, nemusí existovat související entita OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="5fe9f-412">Byl přidán sloupec **Kurzy,** který zobrazuje kurzy vyučované jednotlivými instruktory.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="5fe9f-413">Další informace o této syntaxi holicího strojku naleznete [v tématu Explicitní přechod řádku.](xref:mvc/views/razor#explicit-line-transition)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="5fe9f-414">Přidán kód, který `class="success"` dynamicky přidává do `tr` prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="5fe9f-415">Tím nastavíte barvu pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="5fe9f-416">Byl přidán nový hypertextový odkaz s názvem **Select**.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="5fe9f-417">Tento odkaz odešle ID vybraného `Index` instruktora metodě a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="5fe9f-418">Spusťte aplikaci a vyberte kartu **Instruktoři.** Na stránce `Location` se zobrazí (kancelář) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="5fe9f-419">Pokud OfficeAssignment' je null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="5fe9f-420">Klikněte na odkaz **Vybrat.**</span><span class="sxs-lookup"><span data-stu-id="5fe9f-420">Click on the **Select** link.</span></span> <span data-ttu-id="5fe9f-421">Styl řádku se změní.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="5fe9f-422">Přidat kurzy vyučované vybraným instruktorem</span><span class="sxs-lookup"><span data-stu-id="5fe9f-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="5fe9f-423">Aktualizujte `OnGetAsync` metodu na *pages/instructors/Index.cshtml.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="5fe9f-424">Přidat`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="5fe9f-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="5fe9f-425">Zkontrolujte aktualizovaný dotaz:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="5fe9f-426">Předchozí dotaz přidá `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="5fe9f-427">Následující kód se spustí, když`id != null`je vybrán instruktor ( ).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="5fe9f-428">Vybraný instruktor je načten ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="5fe9f-429">`Courses` Vlastnost modelu zobrazení je načtena `Course` s entitami `CourseAssignments` z navigační vlastnosti tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="5fe9f-430">Metoda `Where` vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="5fe9f-431">V předchozí `Where` metodě je `Instructor` vrácena pouze jedna entita.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="5fe9f-432">Metoda `Single` převede kolekci na `Instructor` jednu entitu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="5fe9f-433">Entita `Instructor` poskytuje `CourseAssignments` přístup k vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="5fe9f-434">`CourseAssignments`poskytuje přístup k `Course` propojeným subjektům.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instruktor-kurzy m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5fe9f-436">Metoda `Single` se používá v kolekci, pokud kolekce má pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="5fe9f-437">Metoda `Single` vyvolá výjimku, pokud je kolekce prázdná nebo pokud existuje více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="5fe9f-438">Alternativou `SingleOrDefault`je , která vrátí výchozí hodnotu (null v tomto případě), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="5fe9f-439">Použití `SingleOrDefault` na prázdnou kolekci:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="5fe9f-440">Výsledkem je výjimka (z `Courses` pokusu o nalezení vlastnosti na nulový odkaz).</span><span class="sxs-lookup"><span data-stu-id="5fe9f-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="5fe9f-441">Zpráva o výjimce by méně jasně označovat příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="5fe9f-442">Následující kód naplní `Enrollments` vlastnost modelu zobrazení, když je vybrán kurz:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="5fe9f-443">Na konec *stránky/instruktoři/index.cshtml* Holicí strojek přidejte následující značky:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="5fe9f-444">Předchozí značky zobrazí seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="5fe9f-445">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-445">Test the app.</span></span> <span data-ttu-id="5fe9f-446">Klikněte na odkaz **Vybrat** na stránce instruktoři.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="5fe9f-447">Zobrazit údaje o studentech</span><span class="sxs-lookup"><span data-stu-id="5fe9f-447">Show student data</span></span>

<span data-ttu-id="5fe9f-448">V této části je aplikace aktualizována tak, aby zobrazovala data studentů pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="5fe9f-449">Aktualizujte dotaz `OnGetAsync` v metodě v *pages/instructors/Index.cshtml.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="5fe9f-450">Aktualizovat *stránky/instruktoři/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="5fe9f-451">Na konec souboru přidejte následující značky:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="5fe9f-452">Předchozí značky zobrazí seznam studentů, kteří jsou zapsáni ve vybraném kurzu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="5fe9f-453">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="5fe9f-454">Výběrem kurzu zobrazíte seznam zapsaných studentů a jejich známky.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-454">Select a course to see the list of enrolled students and their grades.</span></span>

![Instruktoři Index stránky instruktor a kurz vybrán](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="5fe9f-456">Použití jednoduchého</span><span class="sxs-lookup"><span data-stu-id="5fe9f-456">Using Single</span></span>

<span data-ttu-id="5fe9f-457">Metoda `Single` může předat `Where` v podmínce `Where` namísto volání metody samostatně:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="5fe9f-458">Předchozí `Single` přístup poskytuje žádné výhody `Where`nad pomocí .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="5fe9f-459">Někteří vývojáři `Single` dávají přednost stylu přístupu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="5fe9f-460">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="5fe9f-460">Explicit loading</span></span>

<span data-ttu-id="5fe9f-461">Aktuální kód určuje nedočkavé načítání pro `Enrollments` a `Students`:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="5fe9f-462">Předpokládejme, že uživatelé zřídka chtějí zobrazit zápisy v kurzu.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="5fe9f-463">V takovém případě optimalizace by bylo načíst pouze data zápisu, pokud je požadováno.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="5fe9f-464">V této části `OnGetAsync` je aktualizován použít `Enrollments` explicitní `Students`načítání a .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="5fe9f-465">`OnGetAsync` Aktualizujte následující kód:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="5fe9f-466">Předchozí kód klesne *ThenInclude* volání metody pro zápis a data studenta.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="5fe9f-467">Pokud je vybrán kurz, zvýrazněný kód se načte:</span><span class="sxs-lookup"><span data-stu-id="5fe9f-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="5fe9f-468">Entity `Enrollment` pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="5fe9f-469">Entity `Student` pro každý `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="5fe9f-470">Všimněte si předchozíkód `.AsNoTracking()`komentáře ven .</span><span class="sxs-lookup"><span data-stu-id="5fe9f-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="5fe9f-471">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="5fe9f-472">Otestujete aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-472">Test the app.</span></span> <span data-ttu-id="5fe9f-473">Z pohledu uživatelů se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="5fe9f-474">Další kurz ukazuje, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="5fe9f-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5fe9f-475">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5fe9f-475">Additional resources</span></span>

* [<span data-ttu-id="5fe9f-476">Verze tohoto výukového programu na YouTube (část1)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="5fe9f-477">Verze tohoto výukového programu na YouTube (část2)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="5fe9f-478">[Předchozí](xref:data/ef-rp/complex-data-model)
>[další](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="5fe9f-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
