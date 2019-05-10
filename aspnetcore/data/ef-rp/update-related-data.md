---
title: Stránky Razor s EF Core v ASP.NET Core – aktualizace souvisejících dat – 7 8
author: rick-anderson
description: V tomto kurzu budete aktualizovat souvisejících dat prostřednictvím aktualizace pole cizích klíčů a navigační vlastnosti.
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: b996e7f9c8e422060f6378a6004a53906e2af7c7
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087086"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="a0d6c-103">Stránky Razor s EF Core v ASP.NET Core – aktualizace souvisejících dat – 7 8</span><span class="sxs-lookup"><span data-stu-id="a0d6c-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="a0d6c-104">Podle [Petr Dykstra](https://github.com/tdykstra), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a0d6c-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="a0d6c-105">Tento kurz ukazuje, aktualizuje se související data.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-105">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="a0d6c-106">Pokud narazíte na potíže nelze vyřešit, [stažení nebo zobrazení dokončené aplikace.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="a0d6c-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="a0d6c-107">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="a0d6c-108">Následující ilustrace ukazuje některé dokončených stránek.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-108">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="a0d6c-109">![Stránky pro úpravu kurzu](update-related-data/_static/course-edit.png)
![kurzů vedených upravit stránku](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="a0d6c-109">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="a0d6c-110">Zkontrolujte a otestujte kurzu stránky vytvořit a upravit.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-110">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="a0d6c-111">Vytvořte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-111">Create a new course.</span></span> <span data-ttu-id="a0d6c-112">Oddělení se vybírá podle jeho primárního klíče (celé číslo), nikoli jeho název.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-112">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="a0d6c-113">Upravte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-113">Edit the new course.</span></span> <span data-ttu-id="a0d6c-114">Po dokončení testování, odstranění nový kurz.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-114">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="a0d6c-115">Vytvořte základní třídu sdílet společný kód</span><span class="sxs-lookup"><span data-stu-id="a0d6c-115">Create a base class to share common code</span></span>

<span data-ttu-id="a0d6c-116">Stránky kurzy/Create a kurzy nebo upravit třeba seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-116">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="a0d6c-117">Vytvořit *Pages/Courses/DepartmentNamePageModel.cshtml.cs* základní třída pro vytvoření a úprava stránky:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-117">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="a0d6c-118">Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) tak, aby obsahovala seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-118">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="a0d6c-119">Pokud `selectedDepartment` není zadána, oddělení je vybraný v `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-119">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="a0d6c-120">Vytvoření a úprava tříd modelu stránky odvodí z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-120">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="a0d6c-121">Přizpůsobení stránek kurzy</span><span class="sxs-lookup"><span data-stu-id="a0d6c-121">Customize the Courses Pages</span></span>

<span data-ttu-id="a0d6c-122">Při vytvoření nové entity kurzu musí mít relaci k existující oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-122">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="a0d6c-123">Chcete-li přidat při vytváření kurz oddělení, obsahuje základní třída pro vytvoření a úprava rozevíracího seznamu pro výběr z oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-123">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="a0d6c-124">Nastaví rozevíracího seznamu `Course.DepartmentID` vlastnosti cizího klíče (Cizíklíč).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-124">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="a0d6c-125">EF Core používá `Course.DepartmentID` FK načíst `Department` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-125">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Vytvořit kurz](update-related-data/_static/ddl.png)

<span data-ttu-id="a0d6c-127">Aktualizace modelu stránka vytvořit s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-127">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="a0d6c-128">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-128">The preceding code:</span></span>

* <span data-ttu-id="a0d6c-129">Je odvozen od `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-129">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="a0d6c-130">Používá `TryUpdateModelAsync` zabránit [overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-130">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="a0d6c-131">Nahradí `ViewData["DepartmentID"]` s `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-131">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="a0d6c-132">`ViewData["DepartmentID"]` nahrazuje se silnými typy `DepartmentNameSL`.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-132">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="a0d6c-133">Silného typu modely se dává přednost před slabě typované.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-133">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="a0d6c-134">Další informace najdete v tématu [slabě typované data (ViewData a objekt ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-134">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="a0d6c-135">Aktualizace kurzů vytvořit stránku</span><span class="sxs-lookup"><span data-stu-id="a0d6c-135">Update the Courses Create page</span></span>

<span data-ttu-id="a0d6c-136">Aktualizace *Pages/Courses/Create.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-136">Update *Pages/Courses/Create.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="a0d6c-137">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-137">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="a0d6c-138">Změní titulek z **DepartmentID** k **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-138">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="a0d6c-139">Nahradí `"ViewBag.DepartmentID"` s `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-139">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="a0d6c-140">Přidá možnost "Vybrat oddělení".</span><span class="sxs-lookup"><span data-stu-id="a0d6c-140">Adds the "Select Department" option.</span></span> <span data-ttu-id="a0d6c-141">Tato změna vykreslí "Vyberte oddělení" místo první oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-141">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="a0d6c-142">Přidá ověřovací zprávu, pokud není vybrána oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-142">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="a0d6c-143">Stránka Razor používá [vyberte pomocné rutiny značky](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="a0d6c-143">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="a0d6c-144">Testovací stránka vytvořit.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-144">Test the Create page.</span></span> <span data-ttu-id="a0d6c-145">Zobrazí se stránka vytvořit název oddělení, místo ID oddělení.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-145">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="a0d6c-146">Aktualizace kurzů upravit stránku.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-146">Update the Courses Edit page.</span></span>

<span data-ttu-id="a0d6c-147">Aktualizace modelu upravit stránku s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-147">Update the edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="a0d6c-148">Změny jsou podobné těm, které jsou provedeny v modelu vytvořit stránku.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-148">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="a0d6c-149">V předchozím kódu `PopulateDepartmentsDropDownList` průchody v ID oddělení, které vyberte oddělení uvedli v seznamu, rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-149">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="a0d6c-150">Aktualizace *Pages/Courses/Edit.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-150">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="a0d6c-151">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-151">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="a0d6c-152">Zobrazuje ID kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-152">Displays the course ID.</span></span> <span data-ttu-id="a0d6c-153">Obecně se nezobrazí primární klíč (PK) entity.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-153">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="a0d6c-154">PKs jsou obvykle nemá význam pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-154">PKs are usually meaningless to users.</span></span> <span data-ttu-id="a0d6c-155">V tomto případě primárnímu Klíči je číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-155">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="a0d6c-156">Změní titulek z **DepartmentID** k **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-156">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="a0d6c-157">Nahradí `"ViewBag.DepartmentID"` s `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-157">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="a0d6c-158">Tato stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-158">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="a0d6c-159">Přidávání `<label>` pomocná rutina značky `asp-for="Course.CourseID"` nebude eliminuje nutnost skryté pole.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-159">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="a0d6c-160">`<input type="hidden">` je třeba číslo kurzu mají být zahrnuty v odeslaných dat, když uživatel klikne **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-160">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="a0d6c-161">Otestujte aktualizovaný kód.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-161">Test the updated code.</span></span> <span data-ttu-id="a0d6c-162">Vytvářet, upravovat a odstraňovat kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-162">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="a0d6c-163">Podrobné informace o přidání AsNoTracking a odstraňovat modely stránky</span><span class="sxs-lookup"><span data-stu-id="a0d6c-163">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="a0d6c-164">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zlepšit výkon při sledování není povinné.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-164">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="a0d6c-165">Přidat `AsNoTracking` modelu stránku odstranit a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-165">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="a0d6c-166">Následující kód ukazuje aktualizovaného modelu odstranit stránky:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-166">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="a0d6c-167">Aktualizace `OnGetAsync` metodu *Pages/Courses/Details.cshtml.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-167">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="a0d6c-168">Úprava stránky Delete a podrobnosti</span><span class="sxs-lookup"><span data-stu-id="a0d6c-168">Modify the Delete and Details pages</span></span>

<span data-ttu-id="a0d6c-169">Aktualizace stránky Razor odstranit následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-169">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="a0d6c-170">Proveďte stejné změny na stránku podrobností.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-170">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="a0d6c-171">Testování stránek kurzu</span><span class="sxs-lookup"><span data-stu-id="a0d6c-171">Test the Course pages</span></span>

<span data-ttu-id="a0d6c-172">Test vytvořit, upravit, podrobnosti a odstranění.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-172">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="a0d6c-173">Aktualizace stránek instruktorem</span><span class="sxs-lookup"><span data-stu-id="a0d6c-173">Update the instructor pages</span></span>

<span data-ttu-id="a0d6c-174">V dalších částech aktualizace kurzů vedených stránek.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-174">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="a0d6c-175">Přidat umístění pro office</span><span class="sxs-lookup"><span data-stu-id="a0d6c-175">Add office location</span></span>

<span data-ttu-id="a0d6c-176">Při úpravě záznamu instruktorem, můžete aktualizovat přiřazení kanceláře instruktorem.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-176">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="a0d6c-177">`Instructor` Entita má vztah k nule nebo jednom s `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-177">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="a0d6c-178">Kód kurzů vedených musí zpracovat:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-178">The instructor code must handle:</span></span>

* <span data-ttu-id="a0d6c-179">Pokud uživatel vymaže přiřazení kanceláře, odstraňte `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-179">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="a0d6c-180">Pokud uživatel zadá přiřazení kanceláře a byla prázdná, vytvořte nový `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-180">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="a0d6c-181">Pokud uživatel změní přiřazení kanceláře, aktualizujte `OfficeAssignment` entity.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-181">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="a0d6c-182">Aktualizace modelu Instruktoři upravit stránku s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-182">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="a0d6c-183">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-183">The preceding code:</span></span>

* <span data-ttu-id="a0d6c-184">Získá aktuální `Instructor` entit z databáze pomocí předběžné načítání pro `OfficeAssignment` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-184">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="a0d6c-185">Aktualizuje načtený `Instructor` entity s hodnotami z vazače modelu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-185">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="a0d6c-186">`TryUpdateModel` zabraňuje [overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-186">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="a0d6c-187">Pokud office umístění je prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-187">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="a0d6c-188">Když `Instructor.OfficeAssignment` je null, použije příslušného řádku v `OfficeAssignment` tabulce se odstraní.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-188">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="a0d6c-189">Aktualizace stránky pro úpravu instruktorem</span><span class="sxs-lookup"><span data-stu-id="a0d6c-189">Update the instructor Edit page</span></span>

<span data-ttu-id="a0d6c-190">Aktualizace *Pages/Instructors/Edit.cshtml* umístěním office:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-190">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="a0d6c-191">Ověřte, že můžete změnit umístění služby Instruktoři office.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-191">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="a0d6c-192">Přidat přiřazení kurzu stránky pro úpravu instruktorem</span><span class="sxs-lookup"><span data-stu-id="a0d6c-192">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="a0d6c-193">Instruktoři může představuje libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-193">Instructors may teach any number of courses.</span></span> <span data-ttu-id="a0d6c-194">V této části přidáte změnit přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-194">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="a0d6c-195">Následující obrázek ukazuje aktualizované kurzů vedených stránky pro úpravu:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-195">The following image shows the updated instructor Edit page:</span></span>

![Stránky pro úpravu instruktorem s kurzy](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="a0d6c-197">`Course` a `Instructor` má vztah mnoho mnoho.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-197">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="a0d6c-198">Přidávat a odebírat vztahy, přidávat a odebírat entit na základě `CourseAssignments` připojit k sadě entit.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-198">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="a0d6c-199">Zaškrtávací políčka Povolit změny kurzy, které je přiřazeno instruktorem.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-199">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="a0d6c-200">Zaškrtávací políčko se zobrazí na každé kurz v databázi.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-200">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="a0d6c-201">Kurzy, které je přiřazeno kurzů vedených nebyly zvoleny.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-201">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="a0d6c-202">Uživatel může vyberte nebo zrušte zaškrtnutí políček, chcete-li změnit přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-202">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="a0d6c-203">Kdyby mnohem větší počet kurzů:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-203">If the number of courses were much greater:</span></span>

* <span data-ttu-id="a0d6c-204">Pravděpodobně použijete jiné uživatelské rozhraní zobrazit kurzy.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-204">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="a0d6c-205">Metoda manipulace s spojení entity k vytvoření nebo odstranění vztahů nezměnila.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-205">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="a0d6c-206">Přidání tříd pro podporu vytvářet a upravovat stránky instruktorem</span><span class="sxs-lookup"><span data-stu-id="a0d6c-206">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="a0d6c-207">Vytvoření *SchoolViewModels/AssignedCourseData.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-207">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="a0d6c-208">`AssignedCourseData` Třída obsahuje data k vytvoření zaškrtnutí políček u přiřazené kurzy podle instruktorem.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-208">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="a0d6c-209">Vytvořte *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* základní třídy:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-209">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="a0d6c-210">`InstructorCoursesPageModel` Je základní třídou budete používat pro úpravy a vytváření modelů stránky.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-210">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="a0d6c-211">`PopulateAssignedCourseData` přečte všechny `Course` entity k naplnění `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-211">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="a0d6c-212">Pro každý kurz kód nastaví `CourseID`, název a jestli se kurzů vedených přiřazen ke kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-212">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="a0d6c-213">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) slouží k vytvoření efektivního vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-213">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="a0d6c-214">Model stránky Instruktoři úpravy</span><span class="sxs-lookup"><span data-stu-id="a0d6c-214">Instructors Edit page model</span></span>

<span data-ttu-id="a0d6c-215">Aktualizace modelu kurzů vedených upravit stránku s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-215">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="a0d6c-216">Předchozí kód zpracovává změny přiřazení sady office.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-216">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="a0d6c-217">Aktualizace kurzů vedených zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-217">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="a0d6c-218">Při vkládání kódu v sadě Visual Studio konce řádků se mění tak, aby kód přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-218">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="a0d6c-219">Stisknutím klávesy Ctrl + Z jednou vrátit zpět, automatického formátování.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-219">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="a0d6c-220">CTRL + Z opravy konce řádků, tak, aby vypadají se zobrazí tady.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-220">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="a0d6c-221">Odsazení nemusí být dokonalý, ale `@</tr><tr>`, `@:<td>`, `@:</td>`, a `@:</tr>` řádky musí být na jednom řádku jak je znázorněno.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-221">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="a0d6c-222">Blok vybrali nový kód a stisknutím klávesy Tab třikrát na řádek nahoru nový kód existující kód.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-222">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="a0d6c-223">Hlasujte nebo zkontrolujte stav tuto chybu [s tímto odkazem](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="a0d6c-223">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="a0d6c-224">Předchozí kód vytvoří tabulku HTML, který obsahuje tři sloupce.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-224">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="a0d6c-225">Zaškrtávací políčko a popisek obsahující číslo kurzu a název má každý sloupec.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-225">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="a0d6c-226">Všechna zaškrtávací políčka mají stejný název ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="a0d6c-226">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="a0d6c-227">Použití stejného názvu informuje vazač modelu pro s nimi zacházet jako se skupinou.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-227">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="a0d6c-228">Atribut hodnotu každého zaškrtávacího políčka je nastaven na `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-228">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="a0d6c-229">Na stránce, když se pošle vazače modelu předává pole, které se skládá z `CourseID` hodnoty pro pouze pole, které jsou vybrány.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-229">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="a0d6c-230">Když zaškrtávací políčka jsou zpočátku vykresleno, kurzy, které jsou přiřazeny kurzů vedených kontrole atributů.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-230">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="a0d6c-231">Spusťte aplikaci a otestovat aktualizované Instruktoři stránky pro úpravu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-231">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="a0d6c-232">Změňte některá přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-232">Change some course assignments.</span></span> <span data-ttu-id="a0d6c-233">Změny se projeví na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-233">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="a0d6c-234">Poznámka: Přístup provádět upravovat data kurzu kurzů vedených funguje dobře, když je omezený počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-234">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="a0d6c-235">Pro kolekce, které jsou mnohem větší různé uživatelské rozhraní a jinou metodu aktualizace by spotřebovat a efektivní.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-235">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="a0d6c-236">Stránka pro vytvoření Instruktoři aktualizace</span><span class="sxs-lookup"><span data-stu-id="a0d6c-236">Update the instructors Create page</span></span>

<span data-ttu-id="a0d6c-237">Aktualizace kurzů vedených vytvořit stránku model s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-237">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="a0d6c-238">Předchozí kód je podobný *Pages/Instructors/Edit.cshtml.cs* kódu.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-238">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="a0d6c-239">Aktualizace stránky Razor vytvořit kurzů vedených následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-239">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="a0d6c-240">Testovací stránka vytvořit instruktorem.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-240">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="a0d6c-241">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="a0d6c-241">Update the Delete page</span></span>

<span data-ttu-id="a0d6c-242">Aktualizace modelu odstranění stránky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-242">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="a0d6c-243">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="a0d6c-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="a0d6c-244">Používá předběžné načítání pro `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-244">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="a0d6c-245">`CourseAssignments` musí být zahrnut nebo se neodstranily při odstranění instruktorem.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-245">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="a0d6c-246">Abyste nemuseli přečtěte si je, nakonfigurujte kaskádové odstranění v databázi.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-246">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="a0d6c-247">Pokud instruktorem, která se má odstranit je přiřazen jako správce z jakékoli oddělení, odebere z těchto oddělení přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="a0d6c-247">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0d6c-248">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a0d6c-248">Additional resources</span></span>

* [<span data-ttu-id="a0d6c-249">YouTube verzi tohoto kurzu (část 1)</span><span class="sxs-lookup"><span data-stu-id="a0d6c-249">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="a0d6c-250">YouTube verzi tohoto kurzu (část 2)</span><span class="sxs-lookup"><span data-stu-id="a0d6c-250">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="a0d6c-251">[Předchozí](xref:data/ef-rp/read-related-data)
> [další](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="a0d6c-251">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>
