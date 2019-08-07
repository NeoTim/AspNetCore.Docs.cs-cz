---
title: Razor Pages s EF Core ve ASP.NET Core – data související s čtením – 6 z 8
author: rick-anderson
description: V tomto kurzu si přečtete a zobrazíte související data – to znamená data, která Entity Framework načíst do vlastností navigace.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/read-related-data
ms.openlocfilehash: 93fbb4741f476368d75d23162d6e2597de7b263e
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819913"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="02970-103">Razor Pages s EF Core ve ASP.NET Core – data související s čtením – 6 z 8</span><span class="sxs-lookup"><span data-stu-id="02970-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="02970-104">Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02970-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="02970-105">V tomto kurzu se budou číst a zobrazovat související data.</span><span class="sxs-lookup"><span data-stu-id="02970-105">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="02970-106">Související data jsou data, která EF Core načíst do vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="02970-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="02970-107">Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="02970-107">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="02970-108">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="02970-108">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="02970-109">Na následujících obrázcích je znázorněno hotové stránky pro tento kurz:</span><span class="sxs-lookup"><span data-stu-id="02970-109">The following illustrations show the completed pages for this tutorial:</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="02970-112">Eager, explicitní a opožděné načítání souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="02970-112">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="02970-113">Existuje několik způsobů, jak může EF Core načíst související data do navigačních vlastností entity:</span><span class="sxs-lookup"><span data-stu-id="02970-113">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="02970-114">[Eager načítání](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="02970-114">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="02970-115">Eager načítání je v případě, že dotaz pro jeden typ entity také načte související entity.</span><span class="sxs-lookup"><span data-stu-id="02970-115">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="02970-116">Při čtení entity se načtou související data.</span><span class="sxs-lookup"><span data-stu-id="02970-116">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="02970-117">To obvykle vede k tomu, že se vytvoří dotaz s jedním spojením, který načte všechna potřebná data.</span><span class="sxs-lookup"><span data-stu-id="02970-117">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="02970-118">EF Core bude vydávat více dotazů pro některé typy načítání Eager.</span><span class="sxs-lookup"><span data-stu-id="02970-118">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="02970-119">Vystavení více dotazů může být efektivnější než u některých dotazů v EF6, kde existoval jeden dotaz.</span><span class="sxs-lookup"><span data-stu-id="02970-119">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="02970-120">Eager načítání je zadáno pomocí `Include` metod a. `ThenInclude`</span><span class="sxs-lookup"><span data-stu-id="02970-120">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Příklad načtení Eager](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="02970-122">Eager načítání odesílá více dotazů, pokud je součástí navigace kolekce:</span><span class="sxs-lookup"><span data-stu-id="02970-122">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="02970-123">Jeden dotaz pro hlavní dotaz</span><span class="sxs-lookup"><span data-stu-id="02970-123">One query for the main query</span></span> 
  * <span data-ttu-id="02970-124">Jeden dotaz pro každou kolekci "Edge" ve stromu zatížení.</span><span class="sxs-lookup"><span data-stu-id="02970-124">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="02970-125">Oddělte dotazy `Load`pomocí: Data lze načíst v samostatných dotazech a EF Core "opravuje" navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="02970-125">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="02970-126">"opravami" se rozumí, že EF Core automaticky naplnit navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="02970-126">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="02970-127">Samostatné dotazy se `Load` podobají EXPLICT načítání než Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="02970-127">Separate queries with `Load` is more like explict loading than eager loading.</span></span>

  ![Příklad samostatných dotazů](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="02970-129">Poznámka: EF Core automaticky opravuje navigační vlastnosti na jakékoli další entity, které byly dříve načteny do instance kontextu.</span><span class="sxs-lookup"><span data-stu-id="02970-129">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="02970-130">I v případě, že data pro vlastnost navigace *nejsou* explicitně zahrnutá, může být tato vlastnost i nadále naplněna, pokud byly některé nebo všechny související entity dříve načteny.</span><span class="sxs-lookup"><span data-stu-id="02970-130">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="02970-131">[Explicitní načítání](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="02970-131">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="02970-132">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="02970-132">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="02970-133">Aby bylo možné načíst související data v případě potřeby, je nutné napsat kód.</span><span class="sxs-lookup"><span data-stu-id="02970-133">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="02970-134">Explicitní načítání pomocí samostatných dotazů má za následek více dotazů odeslaných do databáze.</span><span class="sxs-lookup"><span data-stu-id="02970-134">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="02970-135">Při explicitním načítání kód určuje navigační vlastnosti, které mají být načteny.</span><span class="sxs-lookup"><span data-stu-id="02970-135">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="02970-136">K provedení explicitního načítání použijte metodu.`Load`</span><span class="sxs-lookup"><span data-stu-id="02970-136">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="02970-137">Příklad:</span><span class="sxs-lookup"><span data-stu-id="02970-137">For example:</span></span>

  ![Příklad explicitního načtení](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="02970-139">[Opožděné načítání](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="02970-139">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="02970-140">[Opožděné načítání bylo přidáno do EF Core ve verzi 2,1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="02970-140">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="02970-141">Při prvním načtení entity se nenačte související data.</span><span class="sxs-lookup"><span data-stu-id="02970-141">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="02970-142">Při prvním otevření navigační vlastnosti je automaticky načtena data potřebná pro tuto vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="02970-142">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="02970-143">Dotaz se pošle do databáze při prvním otevření navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="02970-143">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="02970-144">`Select` Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="02970-144">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="02970-145">Vytvoří stránku kurzu, která zobrazuje název oddělení.</span><span class="sxs-lookup"><span data-stu-id="02970-145">Create a Course page that displays department name</span></span>

<span data-ttu-id="02970-146">Entita kurzu obsahuje navigační vlastnost, která obsahuje `Department` entitu.</span><span class="sxs-lookup"><span data-stu-id="02970-146">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="02970-147">`Department` Entita obsahuje oddělení, ke kterému je kurz přiřazen.</span><span class="sxs-lookup"><span data-stu-id="02970-147">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="02970-148">Chcete-li zobrazit název přiřazeného oddělení v seznamu kurzů:</span><span class="sxs-lookup"><span data-stu-id="02970-148">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="02970-149">`Name` Získá vlastnost`Department` z entity.</span><span class="sxs-lookup"><span data-stu-id="02970-149">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="02970-150">`Department` Entita pochází`Course.Department` z navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="02970-150">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Samozřejmě. oddělení](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="02970-152">Generování uživatelského rozhraní modelu kurzu</span><span class="sxs-lookup"><span data-stu-id="02970-152">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02970-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02970-153">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="02970-154">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Course` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="02970-154">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="02970-155">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="02970-155">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="02970-156">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="02970-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="02970-157">Předchozí příkaz scaffold `Course` modelu.</span><span class="sxs-lookup"><span data-stu-id="02970-157">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="02970-158">Otevřete projekt v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="02970-158">Open the project in Visual Studio.</span></span>

<span data-ttu-id="02970-159">Otevřete *stránky/kurzy/index. cshtml. cs* a prověřte `OnGetAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="02970-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="02970-160">Modul generování uživatelského rozhraní určený pro `Department` vlastnost navigace Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="02970-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="02970-161">`Include` Metoda určuje Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="02970-161">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="02970-162">Spusťte aplikaci a vyberte odkaz **kurzy** .</span><span class="sxs-lookup"><span data-stu-id="02970-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="02970-163">Sloupec oddělení zobrazí `DepartmentID`, což není užitečné.</span><span class="sxs-lookup"><span data-stu-id="02970-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="02970-164">Aktualizujte `OnGetAsync` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02970-164">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="02970-165">Předchozí kód přidá `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="02970-165">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="02970-166">`AsNoTracking`zlepšuje výkon, protože vracené entity nejsou sledovány.</span><span class="sxs-lookup"><span data-stu-id="02970-166">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="02970-167">Entity nejsou sledovány, protože nejsou aktualizovány v aktuálním kontextu.</span><span class="sxs-lookup"><span data-stu-id="02970-167">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="02970-168">Aktualizovat *stránky/kurzy/index. cshtml* pomocí následujícího zvýrazněného označení:</span><span class="sxs-lookup"><span data-stu-id="02970-168">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="02970-169">V generovaném kódu byly provedeny následující změny:</span><span class="sxs-lookup"><span data-stu-id="02970-169">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="02970-170">Změnili jste záhlaví z indexu na kurzy.</span><span class="sxs-lookup"><span data-stu-id="02970-170">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="02970-171">Byl přidán sloupec **číslo** , který zobrazuje `CourseID` hodnotu vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="02970-171">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="02970-172">Ve výchozím nastavení nejsou primární klíče vygenerované, protože jsou obvykle nevýznamné pro koncové uživatele.</span><span class="sxs-lookup"><span data-stu-id="02970-172">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="02970-173">V tomto případě je však primární klíč smysluplný.</span><span class="sxs-lookup"><span data-stu-id="02970-173">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="02970-174">Změnili jste sloupec **oddělení** , aby se zobrazil název oddělení.</span><span class="sxs-lookup"><span data-stu-id="02970-174">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="02970-175">Kód zobrazí `Name` vlastnost `Department` entity `Department` , která je načtena do navigační vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="02970-175">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="02970-176">Spusťte aplikaci a vyberte kartu **kurzy** , abyste zobrazili seznam s názvy oddělení.</span><span class="sxs-lookup"><span data-stu-id="02970-176">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Stránka s rejstříkem kurzů](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="02970-178">Načítání souvisejících dat pomocí výběru</span><span class="sxs-lookup"><span data-stu-id="02970-178">Loading related data with Select</span></span>

<span data-ttu-id="02970-179">Metoda načte související data `Include` pomocí metody: `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="02970-179">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="02970-180">`Select` Operátor načte pouze související požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="02970-180">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="02970-181">Pro jednotlivé položky, `Department.Name` například používá vnitřní spojení SQL.</span><span class="sxs-lookup"><span data-stu-id="02970-181">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="02970-182">U kolekcí používá jiný přístup k databázi, ale proto `Include` operátor na kolekcích.</span><span class="sxs-lookup"><span data-stu-id="02970-182">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="02970-183">Následující kód načte související data `Select` metodou:</span><span class="sxs-lookup"><span data-stu-id="02970-183">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="02970-184">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="02970-184">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="02970-185">Úplný příklad naleznete v tématu [IndexSelect. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) a [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) .</span><span class="sxs-lookup"><span data-stu-id="02970-185">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="02970-186">Vytvoření stránky instruktory, která zobrazuje kurzy a registrace</span><span class="sxs-lookup"><span data-stu-id="02970-186">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="02970-187">V této části se vytvoří stránka instruktoři.</span><span class="sxs-lookup"><span data-stu-id="02970-187">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="02970-188">![Stránka indexu instruktorů](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="02970-188">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="02970-189">Tato stránka čte a zobrazuje související data následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="02970-189">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="02970-190">Seznam instruktorů zobrazuje související data z `OfficeAssignment` entity (Office na předchozím obrázku).</span><span class="sxs-lookup"><span data-stu-id="02970-190">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="02970-191">Entity `Instructor` a`OfficeAssignment` jsou v relaci typu 1:1 nebo jedna hodnota.</span><span class="sxs-lookup"><span data-stu-id="02970-191">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="02970-192">Pro `OfficeAssignment` entity se používá Eager načítání.</span><span class="sxs-lookup"><span data-stu-id="02970-192">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="02970-193">Eager načítání je obvykle efektivnější, pokud je potřeba zobrazit související data.</span><span class="sxs-lookup"><span data-stu-id="02970-193">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="02970-194">V tomto případě se zobrazí přiřazení kanceláře pro instruktory.</span><span class="sxs-lookup"><span data-stu-id="02970-194">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="02970-195">Když uživatel vybere instruktora (Harui na předchozím obrázku), zobrazí se související `Course` entity.</span><span class="sxs-lookup"><span data-stu-id="02970-195">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="02970-196">Entity `Instructor` a`Course` jsou v relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="02970-196">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="02970-197">Eager načítání se používá pro `Course` entity a jejich související `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="02970-197">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="02970-198">V takovém případě můžou být samostatné dotazy efektivnější, protože potřebujete jenom kurzy pro vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="02970-198">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="02970-199">Tento příklad ukazuje, jak používat Eager načítání pro navigační vlastnosti v entitách, které jsou ve vlastnostech navigace.</span><span class="sxs-lookup"><span data-stu-id="02970-199">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="02970-200">Když uživatel vybere kurz (chemie na předchozím obrázku), zobrazí se související data z `Enrollments` entity.</span><span class="sxs-lookup"><span data-stu-id="02970-200">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="02970-201">Na předchozím obrázku se zobrazí název a stupeň studenta.</span><span class="sxs-lookup"><span data-stu-id="02970-201">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="02970-202">Entity `Course` a`Enrollment` jsou v relaci 1: n.</span><span class="sxs-lookup"><span data-stu-id="02970-202">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="02970-203">Vytvoření modelu zobrazení pro zobrazení indexu instruktora</span><span class="sxs-lookup"><span data-stu-id="02970-203">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="02970-204">Stránka instruktoři zobrazuje data ze tří různých tabulek.</span><span class="sxs-lookup"><span data-stu-id="02970-204">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="02970-205">Vytvoří se model zobrazení, který obsahuje tři entity reprezentující tři tabulky.</span><span class="sxs-lookup"><span data-stu-id="02970-205">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="02970-206">Ve složce *SchoolViewModels* vytvořte *InstructorIndexData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02970-206">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="02970-207">Generování uživatelského rozhraní modelu instruktor</span><span class="sxs-lookup"><span data-stu-id="02970-207">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="02970-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02970-208">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="02970-209">Postupujte podle pokynů v [generování uživatelského rozhraní modelu student](xref:data/ef-rp/intro#scaffold-the-student-model) a použít `Instructor` pro třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="02970-209">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="02970-210">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="02970-210">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="02970-211">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="02970-211">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="02970-212">Předchozí příkaz scaffold `Instructor` modelu.</span><span class="sxs-lookup"><span data-stu-id="02970-212">The preceding command scaffolds the `Instructor` model.</span></span> <span data-ttu-id="02970-213">Spusťte aplikaci a přejděte na stránku instruktoři.</span><span class="sxs-lookup"><span data-stu-id="02970-213">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="02970-214">*Stránky/instruktořis/index. cshtml. cs* nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02970-214">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="02970-215">`OnGetAsync` Metoda přijímá volitelná data směrování pro ID vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="02970-215">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="02970-216">Prověřte dotaz v souboru *Pages/instruktors/index. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="02970-216">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="02970-217">Dotaz obsahuje dvě:</span><span class="sxs-lookup"><span data-stu-id="02970-217">The query has two includes:</span></span>

* <span data-ttu-id="02970-218">`OfficeAssignment`: Zobrazuje se v [zobrazení instruktoři](#IP).</span><span class="sxs-lookup"><span data-stu-id="02970-218">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="02970-219">`CourseAssignments`: Který přináší výukové kurzy.</span><span class="sxs-lookup"><span data-stu-id="02970-219">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="02970-220">Aktualizace stránky indexu instruktorů</span><span class="sxs-lookup"><span data-stu-id="02970-220">Update the instructors Index page</span></span>

<span data-ttu-id="02970-221">Aktualizovat *stránky/instruktory/index. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="02970-221">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="02970-222">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="02970-222">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="02970-223">Aktualizace `page` direktiv z `@page` k `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="02970-223">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="02970-224">`"{id:int?}"`je šablona trasy.</span><span class="sxs-lookup"><span data-stu-id="02970-224">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="02970-225">Šablona trasy mění v adrese URL řetězce dotazů typu Integer k směrování dat.</span><span class="sxs-lookup"><span data-stu-id="02970-225">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="02970-226">Například kliknutím na odkaz **Vybrat** pro instruktora, který má pouze direktivu `@page` , se vytvoří adresa URL jako následující:</span><span class="sxs-lookup"><span data-stu-id="02970-226">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="02970-227">Pokud je `@page "{id:int?}"`direktiva stránky, předchozí adresa URL:</span><span class="sxs-lookup"><span data-stu-id="02970-227">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="02970-228">Nadpis stránky jsou **instruktory**.</span><span class="sxs-lookup"><span data-stu-id="02970-228">Page title is **Instructors**.</span></span>
* <span data-ttu-id="02970-229">Přidání sloupce **Office** , který zobrazí `item.OfficeAssignment.Location` pouze v `item.OfficeAssignment` případě, že není null.</span><span class="sxs-lookup"><span data-stu-id="02970-229">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="02970-230">Vzhledem k tomu, že se jedná o relaci typu 1:1, nemusí se jednat o související entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="02970-230">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="02970-231">Přidali jsme sloupec **kurzy** , ve kterém se zobrazují kurzy výukové každým instruktorem.</span><span class="sxs-lookup"><span data-stu-id="02970-231">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="02970-232">Další informace o této syntaxi Razor naleznete v tématu [ `@:` explicitní přechod řádků](xref:mvc/views/razor#explicit-line-transition-with-) .</span><span class="sxs-lookup"><span data-stu-id="02970-232">See [Explicit line transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="02970-233">Přidaný kód, který `class="success"` dynamicky přidá `tr` do prvku vybraného instruktora.</span><span class="sxs-lookup"><span data-stu-id="02970-233">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="02970-234">Tím se nastaví barva pozadí pro vybraný řádek pomocí třídy Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="02970-234">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="02970-235">Byl přidán nový hypertextový odkaz označený jako **vybraný**.</span><span class="sxs-lookup"><span data-stu-id="02970-235">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="02970-236">Tento odkaz pošle vybrané ID instruktoru do `Index` metody a nastaví barvu pozadí.</span><span class="sxs-lookup"><span data-stu-id="02970-236">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="02970-237">Spusťte aplikaci a vyberte kartu **instruktory** . Stránka zobrazuje `Location` (Office) ze související `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="02970-237">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="02970-238">Pokud má OfficeAssignment hodnotu null, zobrazí se prázdná buňka tabulky.</span><span class="sxs-lookup"><span data-stu-id="02970-238">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

![Žádná vybraná stránka indexu instruktorů](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="02970-240">Klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="02970-240">Click on the **Select** link.</span></span> <span data-ttu-id="02970-241">Styl řádku se změní.</span><span class="sxs-lookup"><span data-stu-id="02970-241">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="02970-242">Přidat výukové kurzy podle vybraného instruktora</span><span class="sxs-lookup"><span data-stu-id="02970-242">Add courses taught by selected instructor</span></span>

<span data-ttu-id="02970-243">Aktualizujte metodu na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem: `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="02970-243">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="02970-244">Přidávání`public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="02970-244">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="02970-245">Projděte si aktualizovaný dotaz:</span><span class="sxs-lookup"><span data-stu-id="02970-245">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="02970-246">Předchozí dotaz přidá `Department` entity.</span><span class="sxs-lookup"><span data-stu-id="02970-246">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="02970-247">Následující kód se spustí při výběru instruktora (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="02970-247">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="02970-248">Vybraný instruktor se načte ze seznamu instruktorů v modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="02970-248">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="02970-249">`Courses` Vlastnost modelu zobrazení je načtena `Course` s `CourseAssignments` entitami z vlastnosti navigace tohoto instruktora.</span><span class="sxs-lookup"><span data-stu-id="02970-249">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="02970-250">`Where` Metoda vrátí kolekci.</span><span class="sxs-lookup"><span data-stu-id="02970-250">The `Where` method returns a collection.</span></span> <span data-ttu-id="02970-251">V předchozí `Where` metodě se vrátí jenom jedna `Instructor` entita.</span><span class="sxs-lookup"><span data-stu-id="02970-251">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="02970-252">Metoda převede kolekci na jednu `Instructor` entitu. `Single`</span><span class="sxs-lookup"><span data-stu-id="02970-252">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="02970-253">Entita poskytuje přístup `CourseAssignments` k vlastnosti. `Instructor`</span><span class="sxs-lookup"><span data-stu-id="02970-253">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="02970-254">`CourseAssignments`poskytuje přístup k souvisejícím `Course` entitám.</span><span class="sxs-lookup"><span data-stu-id="02970-254">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![M:M instruktory do kurzů](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="02970-256">`Single` Metoda se používá v kolekci, pokud má kolekce pouze jednu položku.</span><span class="sxs-lookup"><span data-stu-id="02970-256">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="02970-257">`Single` Metoda vyvolá výjimku, pokud je kolekce prázdná nebo je-li k dispozici více než jedna položka.</span><span class="sxs-lookup"><span data-stu-id="02970-257">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="02970-258">Alternativa je `SingleOrDefault`, která vrací výchozí hodnotu (v tomto případě null), pokud je kolekce prázdná.</span><span class="sxs-lookup"><span data-stu-id="02970-258">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="02970-259">Použití `SingleOrDefault` v prázdné kolekci:</span><span class="sxs-lookup"><span data-stu-id="02970-259">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="02970-260">Výsledkem je výjimka (při pokusu o nalezení `Courses` vlastnosti v odkazu s hodnotou null).</span><span class="sxs-lookup"><span data-stu-id="02970-260">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="02970-261">Zpráva o výjimce by byla zřetelně označovat příčinu problému.</span><span class="sxs-lookup"><span data-stu-id="02970-261">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="02970-262">Následující kód naplní `Enrollments` vlastnost modelu zobrazení, když je vybraný kurz:</span><span class="sxs-lookup"><span data-stu-id="02970-262">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="02970-263">Na konec stránky */instruktory/index. cshtml* Razor přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="02970-263">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="02970-264">Předchozí kód zobrazuje seznam kurzů souvisejících s instruktorem, když je vybrán instruktor.</span><span class="sxs-lookup"><span data-stu-id="02970-264">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="02970-265">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02970-265">Test the app.</span></span> <span data-ttu-id="02970-266">Na stránce instruktoři klikněte na odkaz **Vybrat** .</span><span class="sxs-lookup"><span data-stu-id="02970-266">Click on a **Select** link on the instructors page.</span></span>

![Vybraná instruktora stránky indexu instruktorů](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a><span data-ttu-id="02970-268">Zobrazit data studenta</span><span class="sxs-lookup"><span data-stu-id="02970-268">Show student data</span></span>

<span data-ttu-id="02970-269">V této části se aplikace aktualizuje, aby zobrazovala údaje o studentech pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="02970-269">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="02970-270">Aktualizujte dotaz v `OnGetAsync` metodě na *stránkách/instruktorech/index. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="02970-270">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="02970-271">Aktualizovat *stránky/instruktory/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="02970-271">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="02970-272">Na konec souboru přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="02970-272">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="02970-273">Předchozí kód zobrazuje seznam studentů, kteří jsou zaregistrovaní ve vybraném kurzu.</span><span class="sxs-lookup"><span data-stu-id="02970-273">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="02970-274">Aktualizujte stránku a vyberte instruktora.</span><span class="sxs-lookup"><span data-stu-id="02970-274">Refresh the page and select an instructor.</span></span> <span data-ttu-id="02970-275">Vyberte kurz, abyste zobrazili seznam zaregistrovaných studentů a jejich třídy.</span><span class="sxs-lookup"><span data-stu-id="02970-275">Select a course to see the list of enrolled students and their grades.</span></span>

![Stránka s indexem instruktorů a vybraným kurzem](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="02970-277">Použití jednoduchých</span><span class="sxs-lookup"><span data-stu-id="02970-277">Using Single</span></span>

<span data-ttu-id="02970-278">Metoda může `Where` předat`Where` podmínku namísto volání metody samostatně: `Single`</span><span class="sxs-lookup"><span data-stu-id="02970-278">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="02970-279">Předchozí `Single` přístup neposkytuje oproti použití `Where`žádné výhody.</span><span class="sxs-lookup"><span data-stu-id="02970-279">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="02970-280">Někteří vývojáři upřednostňují `Single` styl přístupu.</span><span class="sxs-lookup"><span data-stu-id="02970-280">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="02970-281">Explicitní načítání</span><span class="sxs-lookup"><span data-stu-id="02970-281">Explicit loading</span></span>

<span data-ttu-id="02970-282">Aktuální kód určuje Eager načítání pro `Enrollments` a: `Students`</span><span class="sxs-lookup"><span data-stu-id="02970-282">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="02970-283">Předpokládejme, že uživatelé zřídka chtějí v kurzu zobrazit registrace.</span><span class="sxs-lookup"><span data-stu-id="02970-283">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="02970-284">V takovém případě může optimalizace načíst data zápisu pouze v případě, že je požadováno.</span><span class="sxs-lookup"><span data-stu-id="02970-284">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="02970-285">V této části `OnGetAsync` je aktualizována pro použití explicitního `Enrollments` načítání a `Students`.</span><span class="sxs-lookup"><span data-stu-id="02970-285">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="02970-286">Aktualizujte `OnGetAsync` pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="02970-286">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="02970-287">Předchozí kód zruší volání metody *ThenInclude* pro údaje o registraci a studentovi.</span><span class="sxs-lookup"><span data-stu-id="02970-287">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="02970-288">Pokud je vybrán kurz, zvýrazněný kód načte:</span><span class="sxs-lookup"><span data-stu-id="02970-288">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="02970-289">`Enrollment` Entity pro vybraný kurz.</span><span class="sxs-lookup"><span data-stu-id="02970-289">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="02970-290">Entity pro každou `Enrollment`. `Student`</span><span class="sxs-lookup"><span data-stu-id="02970-290">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="02970-291">Všimněte si předcházejících komentářů `.AsNoTracking()`k kódu.</span><span class="sxs-lookup"><span data-stu-id="02970-291">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="02970-292">Navigační vlastnosti lze explicitně načíst pouze pro sledované entity.</span><span class="sxs-lookup"><span data-stu-id="02970-292">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="02970-293">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="02970-293">Test the app.</span></span> <span data-ttu-id="02970-294">V perspektivě uživatelů se aplikace chová stejně jako předchozí verze.</span><span class="sxs-lookup"><span data-stu-id="02970-294">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="02970-295">V dalším kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="02970-295">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02970-296">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="02970-296">Additional resources</span></span>

* [<span data-ttu-id="02970-297">Verze tohoto kurzu pro YouTube (part1)</span><span class="sxs-lookup"><span data-stu-id="02970-297">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="02970-298">Verze tohoto kurzu pro YouTube (Část2)</span><span class="sxs-lookup"><span data-stu-id="02970-298">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="02970-299">[Předchozí](xref:data/ef-rp/complex-data-model)Další
>[](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="02970-299">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>
