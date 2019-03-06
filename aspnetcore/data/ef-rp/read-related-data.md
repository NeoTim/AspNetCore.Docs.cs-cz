---
title: Stránky Razor s EF Core v ASP.NET Core – čtení souvisejících dat – 6 8
author: rick-anderson
description: V tomto kurzu čtení a zobrazení souvisejících dat – to znamená, že data, která načte Entity Framework do navigační vlastnosti.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 140f482e136acf4daba1248fecc87e06db6866f3
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345886"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="b7f5d-103">Stránky Razor s EF Core v ASP.NET Core – čtení souvisejících dat – 6 8</span><span class="sxs-lookup"><span data-stu-id="b7f5d-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="b7f5d-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Jan Macek P](https://twitter.com/thereformedprog), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7f5d-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="b7f5d-105">V tomto kurzu související data načíst a zobrazit.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-105">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="b7f5d-106">Související data jsou data, která načte EF Core do navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="b7f5d-107">Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="b7f5d-107">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="b7f5d-108">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-108">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="b7f5d-109">Dokončené stránky pro účely tohoto kurzu na následujících obrázcích:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-109">The following illustrations show the completed pages for this tutorial:</span></span>

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="b7f5d-112">Nemůžou dočkat, až explicitní a opožděné načtení souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="b7f5d-112">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="b7f5d-113">Existuje několik způsobů, EF Core můžete načíst související data do navigační vlastnosti entity:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-113">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="b7f5d-114">[Předběžné načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-114">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="b7f5d-115">Předběžné načítání je při dotazu na jeden typ entity také načtení souvisejících entit.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-115">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="b7f5d-116">Při čtení je entita, související data načtena.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-116">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="b7f5d-117">Obvykle v důsledku jednoho spojení dotaz, který zkopíruje všechna data, který je nezbytný.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-117">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="b7f5d-118">EF Core vydá pro některé typy nemůžou dočkat, až načítání více dotazů.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-118">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="b7f5d-119">Vydání více dotazů může být efektivnější než v případě u některých dotazů v EF6 tam, kde byla jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-119">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="b7f5d-120">Předběžné načítání je zadán s `Include` a `ThenInclude` metody.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-120">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Předběžné načítání příklad](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="b7f5d-122">Předběžné načítání odešle více dotazů, když je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-122">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="b7f5d-123">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="b7f5d-123">One query for the main query</span></span> 
  * <span data-ttu-id="b7f5d-124">Jeden dotaz pro každou kolekci "edge" ve stromové struktuře zatížení.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-124">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="b7f5d-125">Samostatné dotazy s `Load`: Data můžete obnovit v samostatné dotazy a EF Core termín "opravy" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-125">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="b7f5d-126">"opravy nahoru" znamená, že EF Core automaticky naplní navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-126">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="b7f5d-127">Samostatné dotazy s `Load` se víc explicitní načtení než předběžné načítání.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-127">Separate queries with `Load` is more like explict loading than eager loading.</span></span>

  ![Příklad samostatné dotazy](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="b7f5d-129">Poznámka: EF Core automaticky opravuje vlastnosti navigace s jinými entitami, které byly dříve načtena do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-129">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="b7f5d-130">I v případě, že jsou data pro navigační vlastnost *není* výslovně zahrnuty, vlastnost pořád naplněný, pokud některé nebo všechny související entity byly dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-130">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="b7f5d-131">[Explicitní načtení](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-131">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="b7f5d-132">Pokud entita je nejdřív přečíst, související data nebude načten.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-132">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b7f5d-133">Načíst související data, když je potřeba, musí být kód zapsán.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-133">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="b7f5d-134">Explicitní načtení pomocí samostatné dotazy za následek více dotazy odeslané do databáze.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-134">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="b7f5d-135">S explicitní načtení, určuje kód navigačních vlastností, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-135">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="b7f5d-136">Použití `Load` metodu explicitní načtení.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-136">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="b7f5d-137">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-137">For example:</span></span>

  ![Příklad explicitní načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="b7f5d-139">[Opožděné načtení](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-139">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b7f5d-140">[Opožděné načtení byl přidán do EF Core ve verzi 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-140">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="b7f5d-141">Pokud entita je nejdřív přečíst, související data nebude načten.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-141">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="b7f5d-142">Při prvním přístupu k vlastnosti navigace se automaticky načte data požadovaná pro tuto navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-142">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="b7f5d-143">Bude odeslán dotaz do databáze pokaždé, když vlastnost navigace pracuje poprvé.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-143">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="b7f5d-144">`Select` Operátor načte pouze souvisejících dat, které jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-144">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="b7f5d-145">Vytvoření stránky kurz, který zobrazuje název oddělení</span><span class="sxs-lookup"><span data-stu-id="b7f5d-145">Create a Course page that displays department name</span></span>

<span data-ttu-id="b7f5d-146">Kurz entita obsahuje vlastnost navigace, která obsahuje `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-146">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="b7f5d-147">`Department` Oddělení, která je přiřazena kurz v entitě.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-147">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="b7f5d-148">Chcete-li zobrazit název přiřazený oddělení v seznamu kurzů:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-148">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="b7f5d-149">Získejte `Name` vlastnost z `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-149">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="b7f5d-150">`Department` Entity pocházejí z `Course.Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-150">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![ourse. Oddělení](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>
### <a name="scaffold-the-course-model"></a><span data-ttu-id="b7f5d-152">Vygenerované uživatelské rozhraní modelu kurzu</span><span class="sxs-lookup"><span data-stu-id="b7f5d-152">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7f5d-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7f5d-153">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b7f5d-154">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-154">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7f5d-155">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7f5d-155">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="b7f5d-156">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

------

<span data-ttu-id="b7f5d-157">Předchozí příkaz scaffold `Course` modelu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-157">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="b7f5d-158">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-158">Open the project in Visual Studio.</span></span>

<span data-ttu-id="b7f5d-159">Otevřít *Pages/Courses/Index.cshtml.cs* a prozkoumejte `OnGetAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="b7f5d-160">Předběžné načítání pro zadaný modul generování uživatelského rozhraní `Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="b7f5d-161">`Include` Metody určuje předběžné načítání.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-161">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="b7f5d-162">Spusťte aplikaci a vyberte **kurzy** odkaz.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="b7f5d-163">Zobrazí sloupec oddělení `DepartmentID`, který není užitečné.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="b7f5d-164">Aktualizace `OnGetAsync` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-164">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="b7f5d-165">Předchozí kód přidá `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-165">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="b7f5d-166">`AsNoTracking` zlepší výkon, protože nebudou pro účely vrácených entit.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-166">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="b7f5d-167">Entity, které nejsou sledovat, protože se neaktualizují v rámci aktuálního kontextu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-167">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="b7f5d-168">Aktualizace *Pages/Courses/Index.cshtml* s následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-168">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="b7f5d-169">Na automaticky generovaný kód se provedly následující změny:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-169">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="b7f5d-170">Změnit záhlaví od indexu ke kurzům.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-170">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="b7f5d-171">Přidá **číslo** sloupec, který ukazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-171">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="b7f5d-172">Ve výchozím nastavení nejsou automaticky generovaný primární klíče, protože jsou obvykle nemá význam pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-172">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="b7f5d-173">Ale v tomto případě primární klíč má smysl.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-173">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="b7f5d-174">Změnit **oddělení** sloupec, který se zobrazí název oddělení.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-174">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="b7f5d-175">Zobrazení kódu `Name` vlastnost `Department` entity, který je načten do `Department` navigační vlastnost:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-175">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="b7f5d-176">Spusťte aplikaci a vyberte **kurzy** kartu pro zobrazení seznamu s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-176">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Kurzy indexová stránka](read-related-data/_static/courses-index.png)

<a name="select"></a>
### <a name="loading-related-data-with-select"></a><span data-ttu-id="b7f5d-178">Načítají se související data s vybranými</span><span class="sxs-lookup"><span data-stu-id="b7f5d-178">Loading related data with Select</span></span>

<span data-ttu-id="b7f5d-179">`OnGetAsync` Metoda načte související data se `Include` metody:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-179">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b7f5d-180">`Select` Operátor načte pouze souvisejících dat, které jsou potřeba.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-180">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="b7f5d-181">Pro jednotlivé položky jako `Department.Name` používá SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-181">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="b7f5d-182">Pro kolekce, používá jiný přístup k databázi, ale postupy zločinců `Include` operátor v kolekcích.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-182">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="b7f5d-183">Následující kód načte data související s `Select` metody:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-183">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="b7f5d-184">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-184">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="b7f5d-185">Zobrazit [IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) kompletní příklad.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-185">See [IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="b7f5d-186">Vytvoření stránky školitelů, který ukazuje, kurzy a registrace</span><span class="sxs-lookup"><span data-stu-id="b7f5d-186">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="b7f5d-187">V této části se vytvoří Instruktoři stránka.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-187">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="b7f5d-188">![Instruktoři indexová stránka](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="b7f5d-188">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="b7f5d-189">Tato stránka načte a zobrazí související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-189">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="b7f5d-190">V seznamu instruktorů zobrazí související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-190">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="b7f5d-191">`Instructor` a `OfficeAssignment` entity jsou ve vztahu k nule nebo jednom.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-191">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="b7f5d-192">Předběžné načítání se používá pro `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-192">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="b7f5d-193">Předběžné načítání je obvykle efektivnější, pokud je potřeba zobrazí související data.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-193">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="b7f5d-194">V takovém případě přiřazení sady office pro Instruktoři zobrazují.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-194">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="b7f5d-195">Když uživatel vybere instruktorem (Harui na předchozím obrázku), související `Course` entity jsou zobrazeny.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-195">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="b7f5d-196">`Instructor` a `Course` entity jsou v relaci m: m.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-196">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="b7f5d-197">Předběžné načítání se používá pro `Course` entit a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-197">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="b7f5d-198">Samostatné dotazy v tomto případě může být mnohem efektivnější, protože jsou potřeba jenom kurzů pro vybranou instruktorem.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-198">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="b7f5d-199">Tento příklad ukazuje způsob použití předběžné načítání pro navigační vlastnosti v entitách, které jsou v navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-199">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="b7f5d-200">Když uživatel vybere kurzu (chemie na předchozím obrázku), související data z `Enrollments` entity se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-200">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="b7f5d-201">Na předchozím obrázku jsou zobrazeny jméno studenta a na podnikové úrovni.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-201">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="b7f5d-202">`Course` a `Enrollment` entity jsou v vztah jeden mnoho.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-202">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="b7f5d-203">Vytvoření modelu zobrazení pro zobrazení indexu instruktorem</span><span class="sxs-lookup"><span data-stu-id="b7f5d-203">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="b7f5d-204">Na stránce Instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-204">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="b7f5d-205">Zobrazení modelu je vytvořen, která obsahuje tři entity představující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-205">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="b7f5d-206">V *SchoolViewModels* složku, vytvořte *InstructorIndexData.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-206">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="b7f5d-207">Vygenerované uživatelské rozhraní modelu instruktorem</span><span class="sxs-lookup"><span data-stu-id="b7f5d-207">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b7f5d-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7f5d-208">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="b7f5d-209">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-209">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b7f5d-210">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b7f5d-210">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="b7f5d-211">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-211">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

------

<span data-ttu-id="b7f5d-212">Předchozí příkaz scaffold `Instructor` modelu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-212">The preceding command scaffolds the `Instructor` model.</span></span> <span data-ttu-id="b7f5d-213">Spusťte aplikaci a přejděte na stránku Instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-213">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="b7f5d-214">Nahraďte *Pages/Instructors/Index.cshtml.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-214">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="b7f5d-215">`OnGetAsync` Metoda přijímá data volitelné trasy pro ID vybrané instruktorem.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-215">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="b7f5d-216">Prozkoumat dotaz *Pages/Instructors/Index.cshtml.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-216">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b7f5d-217">Dotaz má dvě zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-217">The query has two includes:</span></span>

* <span data-ttu-id="b7f5d-218">`OfficeAssignment`: Zobrazí v [Instruktoři zobrazení](#IP).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-218">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="b7f5d-219">`CourseAssignments`: Což přináší výukové kurzy.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-219">`CourseAssignments`: Which brings in the courses taught.</span></span>


### <a name="update-the-instructors-index-page"></a><span data-ttu-id="b7f5d-220">Aktualizace Instruktoři indexovou stránku</span><span class="sxs-lookup"><span data-stu-id="b7f5d-220">Update the instructors Index page</span></span>

<span data-ttu-id="b7f5d-221">Aktualizace *Pages/Instructors/Index.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-221">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="b7f5d-222">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-222">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="b7f5d-223">Aktualizace `page` direktiv z `@page` k `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-223">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="b7f5d-224">`"{id:int?}"` je šablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-224">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="b7f5d-225">Šablona trasy změny celé číslo řetězce dotazu v adrese URL data trasy.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-225">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="b7f5d-226">Například, že kliknete na **vyberte** instruktorem s pouze odkaz `@page` – direktiva vytvoří adresu URL podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-226">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

    `http://localhost:1234/Instructors?id=2`

    <span data-ttu-id="b7f5d-227">Po direktivě stránky `@page "{id:int?}"`, předchozí adresa URL je:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-227">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

    `http://localhost:1234/Instructors/2`

* <span data-ttu-id="b7f5d-228">Je název stránky **Instruktoři**.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-228">Page title is **Instructors**.</span></span>
* <span data-ttu-id="b7f5d-229">Přidá **Office** sloupec, který zobrazuje `item.OfficeAssignment.Location` pouze tehdy, pokud `item.OfficeAssignment` není null.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-229">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="b7f5d-230">Protože je to vztah jeden: nula nebo 1, nemusí být související entita OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-230">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="b7f5d-231">Přidá **kurzy** sloupec, který zobrazuje kurzy vedené jednotlivých kurzů vedených.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-231">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="b7f5d-232">Zobrazit [explicitní přechod na řádek s `@:` ](xref:mvc/views/razor#explicit-line-transition-with-) Další informace o tuto syntaxi razor.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-232">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="b7f5d-233">Přidání kódu, která dynamicky přidá `class="success"` k `tr` element vybrané instruktorem.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-233">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="b7f5d-234">Tím se nastaví barvu pozadí vybraného řádku pomocí Bootstrap třídy.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-234">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="b7f5d-235">Přidat nový popisek hypertextový odkaz **vyberte**.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-235">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="b7f5d-236">Tento odkaz odešle vybraný instruktorem ID `Index` metoda a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-236">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="b7f5d-237">Spusťte aplikaci a vyberte **Instruktoři** kartu. Na stránce se zobrazí `Location` (office) z související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-237">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="b7f5d-238">Pokud OfficeAssignment' je null, se zobrazí na prázdné tabulce buňku.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-238">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

![Instruktoři indexovou stránku, kterou nevybere](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="b7f5d-240">Klikněte na **vyberte** odkaz.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-240">Click on the **Select** link.</span></span> <span data-ttu-id="b7f5d-241">Změny stylu řádku.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-241">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="b7f5d-242">Přidat kurzy vedené vybrané instruktorem</span><span class="sxs-lookup"><span data-stu-id="b7f5d-242">Add courses taught by selected instructor</span></span>

<span data-ttu-id="b7f5d-243">Aktualizace `OnGetAsync` metoda *Pages/Instructors/Index.cshtml.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-243">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="b7f5d-244">Add `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="b7f5d-244">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="b7f5d-245">Zkontrolujte aktualizované dotazu:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-245">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="b7f5d-246">Přidá předchozí dotaz `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-246">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="b7f5d-247">Následující kód provede při výběru instruktorem (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-247">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="b7f5d-248">Vybrané kurzů vedených je načten ze seznamu školitelů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-248">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="b7f5d-249">Model zobrazení `Courses` vlastnosti je načtena s `Course` entity z této kurzů vedených `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-249">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="b7f5d-250">`Where` Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-250">The `Where` method returns a collection.</span></span> <span data-ttu-id="b7f5d-251">V předchozím `Where` metody pouze jediný `Instructor` se vrátí entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-251">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="b7f5d-252">`Single` Metoda převede kolekci do jednoho `Instructor` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-252">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="b7f5d-253">`Instructor` Entita poskytuje přístup k `CourseAssignments` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-253">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="b7f5d-254">`CourseAssignments` poskytuje přístup k související `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-254">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M kurzů vedených kurzy](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="b7f5d-256">`Single` Metoda se používá v kolekci, pokud kolekce obsahuje pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-256">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="b7f5d-257">`Single` Metoda vyvolá výjimku, pokud kolekce je prázdná, nebo pokud existuje více než jednu položku.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-257">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="b7f5d-258">Alternativou je `SingleOrDefault`, která vrátí výchozí hodnoty (null v tomto případě) Pokud kolekce je prázdná.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-258">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="b7f5d-259">Pomocí `SingleOrDefault` na prázdnou kolekci:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-259">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="b7f5d-260">Má za následek výjimku (z pokusu o nalezení `Courses` vlastnost na hodnotu Null).</span><span class="sxs-lookup"><span data-stu-id="b7f5d-260">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="b7f5d-261">Zpráva o výjimce by méně jasně ukazovat na příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-261">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="b7f5d-262">Následující kód naplní model zobrazení `Enrollments` vlastnost při výběru kurzu:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-262">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="b7f5d-263">Přidejte následující kód do konce *Pages/Instructors/Index.cshtml* stránky Razor:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-263">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="b7f5d-264">Předchozí kód zobrazí seznam kurzů související s instruktorem, pokud je vybrána instruktorem.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-264">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="b7f5d-265">Testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-265">Test the app.</span></span> <span data-ttu-id="b7f5d-266">Klikněte na **vyberte** odkaz na stránce Instruktoři.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-266">Click on a **Select** link on the instructors page.</span></span>

![Instruktoři Index stránky kurzů vedených vybrané](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a><span data-ttu-id="b7f5d-268">Zobrazení údajů studentů</span><span class="sxs-lookup"><span data-stu-id="b7f5d-268">Show student data</span></span>

<span data-ttu-id="b7f5d-269">V této části se aplikace aktualizuje a zobrazí student data pro vybrané kurzu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-269">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="b7f5d-270">Aktualizovat dotaz `OnGetAsync` metoda ve *Pages/Instructors/Index.cshtml.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-270">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b7f5d-271">Aktualizace *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-271">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="b7f5d-272">Na konec souboru přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-272">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="b7f5d-273">Předchozí kód zobrazí seznam studentů, kteří se zaregistrují ve vybraných kurzů.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-273">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="b7f5d-274">Aktualizujte stránku a vybrat instruktorem.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-274">Refresh the page and select an instructor.</span></span> <span data-ttu-id="b7f5d-275">Vyberte kurzu zobrazíte seznam registrovaná studentů a jejich kvality.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-275">Select a course to see the list of enrolled students and their grades.</span></span>

![Kurzů vedených instruktory Index stránky a vybrali kurzu](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="b7f5d-277">Pomocí jednoho</span><span class="sxs-lookup"><span data-stu-id="b7f5d-277">Using Single</span></span>

<span data-ttu-id="b7f5d-278">`Single` Můžete předat metodě `Where` podmínku namísto volání metody `Where` metoda samostatně:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-278">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="b7f5d-279">Předchozí `Single` přístup nabízí v porovnání s použitím žádné výhody `Where`.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-279">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="b7f5d-280">Někteří vývojáři raději `Single` přistupovat ke stylu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-280">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="b7f5d-281">explicitní načtení</span><span class="sxs-lookup"><span data-stu-id="b7f5d-281">Explicit loading</span></span>

<span data-ttu-id="b7f5d-282">Aktuální kód určuje předběžné načítání pro `Enrollments` a `Students`:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-282">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="b7f5d-283">Předpokládejme, že uživatelé chtějí zřídka naleznete v tématu registrace v kurzu.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-283">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="b7f5d-284">Optimalizace v takovém případě by se pouze načíst data registrací. Pokud je požadováno.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-284">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="b7f5d-285">V této části `OnGetAsync` je aktualizované, aby používaly explicitní načtení `Enrollments` a `Students`.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-285">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="b7f5d-286">Aktualizace `OnGetAsync` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-286">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="b7f5d-287">Předchozí kód klesne *ThenInclude* metoda se volá pro registraci a studentů data.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-287">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="b7f5d-288">Pokud je vybrána kurz, načte zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="b7f5d-288">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="b7f5d-289">`Enrollment` Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-289">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="b7f5d-290">`Student` Entit pro každou `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-290">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="b7f5d-291">Všimněte si, že předchozí na komentářích ke kódu `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-291">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="b7f5d-292">Navigační vlastnosti lze explicitně načíst pouze u sledovaných entit.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-292">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="b7f5d-293">Testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-293">Test the app.</span></span> <span data-ttu-id="b7f5d-294">Z hlediska uživatelů aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-294">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="b7f5d-295">Další kurz ukazuje, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="b7f5d-295">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7f5d-296">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b7f5d-296">Additional resources</span></span>

* [<span data-ttu-id="b7f5d-297">YouTube verzi tohoto kurzu (část 1)</span><span class="sxs-lookup"><span data-stu-id="b7f5d-297">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="b7f5d-298">YouTube verzi tohoto kurzu (část2)</span><span class="sxs-lookup"><span data-stu-id="b7f5d-298">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="b7f5d-299">[Předchozí](xref:data/ef-rp/complex-data-model)
>[další](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="b7f5d-299">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>
