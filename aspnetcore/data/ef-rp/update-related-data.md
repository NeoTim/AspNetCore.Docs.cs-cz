---
title: Razor Stránky s EF core v ASP.NET Core - Aktualizace souvisejících dat - 7 z 8
author: rick-anderson
description: V tomto kurzu aktualizujete související data aktualizací polí cizího klíče a navigačních vlastností.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: fdfdb14ff8414b8bf30f9b95be7ba0a6bcbd2995
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656420"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="37d6a-103">Razor Stránky s EF core v ASP.NET Core - Aktualizace souvisejících dat - 7 z 8</span><span class="sxs-lookup"><span data-stu-id="37d6a-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="37d6a-104">[Tom Dykstra](https://github.com/tdykstra), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="37d6a-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="37d6a-105">Tento kurz ukazuje, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="37d6a-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="37d6a-106">Následující ilustrace znázornit některé z dokončených stránek.</span><span class="sxs-lookup"><span data-stu-id="37d6a-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="37d6a-107">![Stránka Úpravy kurzu](update-related-data/_static/course-edit30.png)
![Stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="37d6a-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="37d6a-108">Aktualizace stránek Pro vytvoření a úpravy kurzu</span><span class="sxs-lookup"><span data-stu-id="37d6a-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="37d6a-109">Kód scaffolded pro stránky Vytvoření a úpravy kurzu má rozevírací seznam oddělení, který zobrazuje ID oddělení (celé číslo).</span><span class="sxs-lookup"><span data-stu-id="37d6a-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="37d6a-110">V rozevíracím seznamu by měl být uveden název oddělení, takže obě tyto stránky potřebují seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="37d6a-111">Chcete-li tento seznam poskytnout, použijte základní třídu pro stránky Vytvořit a upravit.</span><span class="sxs-lookup"><span data-stu-id="37d6a-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="37d6a-112">Vytvoření základní třídy pro vytváření a úpravy kurzu</span><span class="sxs-lookup"><span data-stu-id="37d6a-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="37d6a-113">Vytvořte soubor *Pages/Courses/DepartmentNamePageModel.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="37d6a-114">Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) obsahovat seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="37d6a-115">Pokud `selectedDepartment` je zadán, toto `SelectList`oddělení je vybránv .</span><span class="sxs-lookup"><span data-stu-id="37d6a-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="37d6a-116">Třídy modelu stránky Create and `DepartmentNamePageModel`Edit budou odvozeny z aplikace .</span><span class="sxs-lookup"><span data-stu-id="37d6a-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="37d6a-117">Aktualizace modelu stránky Vytvořit kurz</span><span class="sxs-lookup"><span data-stu-id="37d6a-117">Update the Course Create page model</span></span>

<span data-ttu-id="37d6a-118">Kurz je přiřazen oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="37d6a-119">Základní třída pro stránky Vytvořit a `SelectList` upravit poskytuje pro výběr oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="37d6a-120">Rozevírací seznam, který `SelectList` používá `Course.DepartmentID` nastaví vlastnost cizího klíče (FK).</span><span class="sxs-lookup"><span data-stu-id="37d6a-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="37d6a-121">EF Core `Course.DepartmentID` používá FK `Department` k načtení navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="37d6a-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Vytvořit kurz](update-related-data/_static/ddl30.png)

<span data-ttu-id="37d6a-123">Aktualizovat *stránky/kurzy/Create.cshtml.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="37d6a-124">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="37d6a-124">The preceding code:</span></span>

* <span data-ttu-id="37d6a-125">Pochází z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="37d6a-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="37d6a-126">Používá `TryUpdateModelAsync` se k [zabránění nadměrnému zaúčtování](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="37d6a-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="37d6a-127">Odebere `ViewData["DepartmentID"]`.</span><span class="sxs-lookup"><span data-stu-id="37d6a-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="37d6a-128">`DepartmentNameSL`ze základní třídy je model silného typu a bude použit na stránce Razor.</span><span class="sxs-lookup"><span data-stu-id="37d6a-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="37d6a-129">Modely silného typu jsou upřednostňovány před slabě nadanými.</span><span class="sxs-lookup"><span data-stu-id="37d6a-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="37d6a-130">Další informace naleznete [v tématu Slabě zadaný data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="37d6a-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="37d6a-131">Aktualizace stránky Vytvořit holicí strojek</span><span class="sxs-lookup"><span data-stu-id="37d6a-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="37d6a-132">Aktualizovat *stránky/kurzy/Create.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="37d6a-133">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="37d6a-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="37d6a-134">Změní titulek z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="37d6a-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="37d6a-135">Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).</span><span class="sxs-lookup"><span data-stu-id="37d6a-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="37d6a-136">Přidá možnost Vybrat oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="37d6a-137">Tato změna vykreslí "Vybrat oddělení" v rozevíracím souboru, pokud ještě nebylo vybráno žádné oddělení, nikoli první oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="37d6a-138">Přidá ověřovací zprávu, když není vybráno oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="37d6a-139">Stránka Razor používá pomocníka [pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="37d6a-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="37d6a-140">Otestujte stránku Vytvořit.</span><span class="sxs-lookup"><span data-stu-id="37d6a-140">Test the Create page.</span></span> <span data-ttu-id="37d6a-141">Na stránce Vytvořit se zobrazí název oddělení, nikoli ID oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="37d6a-142">Aktualizace modelu stránky Úpravy kurzu</span><span class="sxs-lookup"><span data-stu-id="37d6a-142">Update the Course Edit page model</span></span>

<span data-ttu-id="37d6a-143">Aktualizovat *stránky/kurzy/Edit.cshtml.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="37d6a-144">Změny jsou podobné těm, které byly provedeny v modelu vytvořit stránku.</span><span class="sxs-lookup"><span data-stu-id="37d6a-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="37d6a-145">V předchozím kódu `PopulateDepartmentsDropDownList` předá id oddělení, které vybere toto oddělení v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="37d6a-146">Aktualizace stránky Úpravy kurzu</span><span class="sxs-lookup"><span data-stu-id="37d6a-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="37d6a-147">Aktualizovat *stránky/kurzy/Edit.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="37d6a-148">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="37d6a-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="37d6a-149">Zobrazí ID kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-149">Displays the course ID.</span></span> <span data-ttu-id="37d6a-150">Obecně se nezobrazuje primární klíč (PK) entity.</span><span class="sxs-lookup"><span data-stu-id="37d6a-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="37d6a-151">PKs jsou obvykle bezvýznamné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="37d6a-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="37d6a-152">V tomto případě pk je číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="37d6a-153">Změní popisek pro oddělení drop-down z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="37d6a-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="37d6a-154">Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).</span><span class="sxs-lookup"><span data-stu-id="37d6a-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="37d6a-155">Stránka obsahuje skryté pole`<input type="hidden">`( ) pro číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="37d6a-156">Přidání `<label>` pomocníka pro `asp-for="Course.CourseID"` značky nevylučuje potřebu skrytého pole.</span><span class="sxs-lookup"><span data-stu-id="37d6a-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="37d6a-157">`<input type="hidden">`je vyžadováno číslo kurzu, které má být zahrnuto do zaúčtovaných dat, když uživatel klikne na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="37d6a-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="37d6a-158">Aktualizace podrobností kurzu a odstranění stránek</span><span class="sxs-lookup"><span data-stu-id="37d6a-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="37d6a-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zlepšit výkon, když sledování není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="37d6a-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="37d6a-160">Aktualizace modelů stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="37d6a-160">Update the Course page models</span></span>

<span data-ttu-id="37d6a-161">Aktualizovat *stránky/kurzy/Delete.cshtml.cs* s následujícím `AsNoTracking`kódem pro přidání :</span><span class="sxs-lookup"><span data-stu-id="37d6a-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="37d6a-162">Proveďte stejnou změnu v souboru *Stránky/Kurzy/Details.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="37d6a-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="37d6a-163">Aktualizace stránek Kurzu Břitva</span><span class="sxs-lookup"><span data-stu-id="37d6a-163">Update the Course Razor pages</span></span>

<span data-ttu-id="37d6a-164">Aktualizujte *stránky/kurzy/Delete.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="37d6a-165">Proveďte stejné změny na stránce Podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="37d6a-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="37d6a-166">Otestujte stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="37d6a-166">Test the Course pages</span></span>

<span data-ttu-id="37d6a-167">Otestujte stránky pro vytváření, úpravy, podrobnosti a odstranění.</span><span class="sxs-lookup"><span data-stu-id="37d6a-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="37d6a-168">Aktualizace stránek pro vytváření a úpravy instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="37d6a-169">Instruktoři mohou vyučovat libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="37d6a-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="37d6a-170">Na následujícím obrázku je zobrazena stránka Úpravinstruktora se zaškrtávacími políčky pole kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Stránka úprav instruktora s kurzy](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="37d6a-172">Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="37d6a-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="37d6a-173">Pro každý kurz v databázi se zobrazí zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="37d6a-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="37d6a-174">Jsou vybrány kurzy, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="37d6a-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="37d6a-175">Uživatel může vybrat nebo vymazat zaškrtávací políčka pro změnu přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="37d6a-176">Pokud počet kurzů byly mnohem větší, jiné uI může fungovat lépe.</span><span class="sxs-lookup"><span data-stu-id="37d6a-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="37d6a-177">Ale způsob řízení mnoha-k-n vztah zde uvedeno by se nezměnilo.</span><span class="sxs-lookup"><span data-stu-id="37d6a-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="37d6a-178">Chcete-li vytvořit nebo odstranit vztahy, manipulujte s entitou spojení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="37d6a-179">Vytvoření kurzu pro přiřazená data kurzů</span><span class="sxs-lookup"><span data-stu-id="37d6a-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="37d6a-180">Vytvořte *SchoolViewModels/AssignedCourseData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="37d6a-181">Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro kurzy přiřazené instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="37d6a-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="37d6a-182">Vytvoření základní třídy modelu stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="37d6a-183">Vytvořte základní třídu *Pages/Instructors/InstructorCoursesPageModel.cs:*</span><span class="sxs-lookup"><span data-stu-id="37d6a-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="37d6a-184">Jedná `InstructorCoursesPageModel` se o základní třídu, kterou použijete pro modely stránek Úpravy a Vytváření.</span><span class="sxs-lookup"><span data-stu-id="37d6a-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="37d6a-185">`PopulateAssignedCourseData`přečte `Course` všechny entity `AssignedCourseDataList`k naplnění .</span><span class="sxs-lookup"><span data-stu-id="37d6a-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="37d6a-186">Pro každý kurz kód `CourseID`nastaví , název a zda je instruktor přiřazen ke kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="37d6a-187">Sada [hashset](/dotnet/api/system.collections.generic.hashset-1) se používá pro efektivní vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="37d6a-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="37d6a-188">Vzhledem k tomu, že stránka Razor nemá kolekci entit Course, `CourseAssignments` vězníní modelu nemůže automaticky aktualizovat vlastnost navigace.</span><span class="sxs-lookup"><span data-stu-id="37d6a-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="37d6a-189">Namísto použití vazače `CourseAssignments` modelu k aktualizaci vlastnosti `UpdateInstructorCourses` navigace, uděláte to v nové metodě.</span><span class="sxs-lookup"><span data-stu-id="37d6a-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="37d6a-190">Proto je třeba `CourseAssignments` vyloučit vlastnost z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="37d6a-191">To nevyžaduje žádnou změnu kódu, `TryUpdateModel` který volá, protože používáte `CourseAssignments` whitelisting přetížení a není v seznamu zahrnutí.</span><span class="sxs-lookup"><span data-stu-id="37d6a-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="37d6a-192">Pokud nebyla zaškrtnuta žádná `UpdateInstructorCourses` políčka, kód `CourseAssignments` v inicializuje vlastnost navigace s prázdnou kolekcí a vrátí:</span><span class="sxs-lookup"><span data-stu-id="37d6a-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="37d6a-193">Kód pak smyčky prostřednictvím všech kurzů v databázi a kontroluje každý kurz proti ty, které jsou aktuálně přiřazeny instruktora versus ty, které byly vybrány na stránce.</span><span class="sxs-lookup"><span data-stu-id="37d6a-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="37d6a-194">Pro usnadnění efektivní vyhledávání, poslední dvě kolekce `HashSet` jsou uloženy v objektech.</span><span class="sxs-lookup"><span data-stu-id="37d6a-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="37d6a-195">Pokud bylo zaškrtnuto políčko pro kurz, ale `Instructor.CourseAssignments` kurz není v navigační vlastnosti, kurz se přidá do kolekce v navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="37d6a-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="37d6a-196">Pokud nebylo zaškrtnuto políčko kurzu, ale kurz je `Instructor.CourseAssignments` v navigační vlastnosti, kurz se odebere z vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="37d6a-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="37d6a-197">Zpracování umístění kanceláře</span><span class="sxs-lookup"><span data-stu-id="37d6a-197">Handle office location</span></span>

<span data-ttu-id="37d6a-198">Jiný vztah, který má stránka pro úpravy zpracovat, je vztah 1:nula nebo jeden, který má entita Instruktor s entitou. `OfficeAssignment`</span><span class="sxs-lookup"><span data-stu-id="37d6a-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="37d6a-199">Kód úprav instruktora musí zpracovávat následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="37d6a-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="37d6a-200">Pokud uživatel vymaže přiřazení `OfficeAssignment` kanceláře, odstraňte entitu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="37d6a-201">Pokud uživatel zadá přiřazení kanceláře a bude prázdné, vytvořte novou `OfficeAssignment` entitu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="37d6a-202">Pokud uživatel změní přiřazení kanceláře, `OfficeAssignment` aktualizujte entitu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="37d6a-203">Aktualizace modelu stránky Úpravy instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="37d6a-204">Aktualizujte *stránky/instruktoři/Edit.cshtml.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="37d6a-205">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="37d6a-205">The preceding code:</span></span>

* <span data-ttu-id="37d6a-206">Získá aktuální `Instructor` entitu z databáze `OfficeAssignment`pomocí `CourseAssignment`eager `CourseAssignment.Course` načítání pro , a navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="37d6a-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="37d6a-207">Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="37d6a-208">`TryUpdateModel`zabraňuje [přeúčtování](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="37d6a-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="37d6a-209">Pokud je umístění kanceláře `Instructor.OfficeAssignment` prázdné, nastaví hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="37d6a-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="37d6a-210">Pokud `Instructor.OfficeAssignment` je null, související `OfficeAssignment` řádek v tabulce je odstraněn.</span><span class="sxs-lookup"><span data-stu-id="37d6a-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="37d6a-211">Volání `PopulateAssignedCourseData` `OnGetAsync` zaškrtávací políčka `AssignedCourseData` pomocí třídy modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="37d6a-212">`UpdateInstructorCourses` Volá, `OnPostAsync` aby se informace z políček použít instruktor entity upravované.</span><span class="sxs-lookup"><span data-stu-id="37d6a-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="37d6a-213">Volání `PopulateAssignedCourseData` `UpdateInstructorCourses` a `OnPostAsync` `TryUpdateModel` v případě selhání.</span><span class="sxs-lookup"><span data-stu-id="37d6a-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="37d6a-214">Tato volání metod obnoví přiřazená data kurzu zadaná na stránce při opětovném zobrazení s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="37d6a-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="37d6a-215">Aktualizace stránky Úpravy břitvy instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="37d6a-216">Aktualizujte *stránky/instruktoři/Edit.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="37d6a-217">Předchozí kód vytvoří tabulku HTML, která má tři sloupce.</span><span class="sxs-lookup"><span data-stu-id="37d6a-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="37d6a-218">Každý sloupec má zaškrtávací políčko a titulek obsahující číslo kurzu a název.</span><span class="sxs-lookup"><span data-stu-id="37d6a-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="37d6a-219">Všechna zaškrtávací políčka mají stejný název ("vybrané kurzy").</span><span class="sxs-lookup"><span data-stu-id="37d6a-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="37d6a-220">Použití stejného názvu informuje pořadač modelu, aby s nimi zacházel jako se skupinou.</span><span class="sxs-lookup"><span data-stu-id="37d6a-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="37d6a-221">Atribut hodnoty každého zaškrtávacího políčka je nastaven na . `CourseID`</span><span class="sxs-lookup"><span data-stu-id="37d6a-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="37d6a-222">Po zaúčtování stránky předává pořadač modelu pole, `CourseID` které se skládá z hodnot pouze pro vybraná zaškrtávací políčka.</span><span class="sxs-lookup"><span data-stu-id="37d6a-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="37d6a-223">Při počátečním vykreslení zaškrtávacích políček jsou vybrány kurzy přiřazené instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="37d6a-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="37d6a-224">Poznámka: Přístup, který zde byl přijat k úpravě dat instruktorského kurzu, funguje dobře, když je omezený počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="37d6a-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="37d6a-225">Pro kolekce, které jsou mnohem větší, jiné ui a různé metody aktualizace by bylo více použitelné a efektivní.</span><span class="sxs-lookup"><span data-stu-id="37d6a-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="37d6a-226">Spusťte aplikaci a otestujte aktualizovanou stránku Úpravinstruktorů.</span><span class="sxs-lookup"><span data-stu-id="37d6a-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="37d6a-227">Změňte některé úkoly kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-227">Change some course assignments.</span></span> <span data-ttu-id="37d6a-228">Změny se projeví na stránce Index.</span><span class="sxs-lookup"><span data-stu-id="37d6a-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="37d6a-229">Aktualizace stránky Vytvoření instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-229">Update the Instructor Create page</span></span>

<span data-ttu-id="37d6a-230">Aktualizujte model stránky Vytvoření instruktora a stránku Holicí strojek s kódem podobným stránce Úpravy:</span><span class="sxs-lookup"><span data-stu-id="37d6a-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="37d6a-231">Otestujte stránku Vytvoření instruktora.</span><span class="sxs-lookup"><span data-stu-id="37d6a-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="37d6a-232">Aktualizace stránky Odstranění instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="37d6a-233">Aktualizujte *stránky/instruktoři/Delete.cshtml.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="37d6a-234">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="37d6a-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="37d6a-235">Používá eager načítání `CourseAssignments` pro navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="37d6a-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="37d6a-236">`CourseAssignments`musí být zahrnuty, nebo nejsou odstraněny, když je instruktor odstraněn.</span><span class="sxs-lookup"><span data-stu-id="37d6a-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="37d6a-237">Chcete-li se vyhnout nutnosti jejich čtení, nakonfigurujte kaskádové odstranění v databázi.</span><span class="sxs-lookup"><span data-stu-id="37d6a-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="37d6a-238">Pokud je instruktor, který má být odstraněn, přiřazen jako správce všech oddělení, odebere přiřazení instruktora z těchto oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="37d6a-239">Spusťte aplikaci a otestujte stránku Odstranit.</span><span class="sxs-lookup"><span data-stu-id="37d6a-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="37d6a-240">Další kroky</span><span class="sxs-lookup"><span data-stu-id="37d6a-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="37d6a-241">[Předchozí kurz](xref:data/ef-rp/read-related-data)
> [Další kurz](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="37d6a-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="37d6a-242">Tento kurz ukazuje aktualizaci souvisejících dat.</span><span class="sxs-lookup"><span data-stu-id="37d6a-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="37d6a-243">Pokud narazíte na problémy, které nemůžete vyřešit, [stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="37d6a-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="37d6a-244">[Stáhnout pokyny](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="37d6a-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="37d6a-245">Následující ilustrace znázorňuje některé dokončené stránky.</span><span class="sxs-lookup"><span data-stu-id="37d6a-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="37d6a-246">![Stránka Úpravy kurzu](update-related-data/_static/course-edit.png)
![Stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="37d6a-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="37d6a-247">Prohlédněte si a otestujte stránky kurzu Vytvořit a upravit.</span><span class="sxs-lookup"><span data-stu-id="37d6a-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="37d6a-248">Vytvořte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="37d6a-248">Create a new course.</span></span> <span data-ttu-id="37d6a-249">Oddělení je vybráno jeho primárním klíčem (celé číslo), nikoli jeho názvem.</span><span class="sxs-lookup"><span data-stu-id="37d6a-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="37d6a-250">Upravte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="37d6a-250">Edit the new course.</span></span> <span data-ttu-id="37d6a-251">Po dokončení testování odstraňte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="37d6a-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="37d6a-252">Vytvoření základní třídy pro sdílení společného kódu</span><span class="sxs-lookup"><span data-stu-id="37d6a-252">Create a base class to share common code</span></span>

<span data-ttu-id="37d6a-253">Kurzy/ Vytvořit a kurzy / Upravit stránky každý potřebovat seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="37d6a-254">Vytvořte základní třídu *Pages/Courses/DepartmentNamePageModel.cshtml.cs* pro stránky Vytvořit a upravit:</span><span class="sxs-lookup"><span data-stu-id="37d6a-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="37d6a-255">Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) obsahovat seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="37d6a-256">Pokud `selectedDepartment` je zadán, toto `SelectList`oddělení je vybránv .</span><span class="sxs-lookup"><span data-stu-id="37d6a-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="37d6a-257">Třídy modelu stránky Create and `DepartmentNamePageModel`Edit budou odvozeny z aplikace .</span><span class="sxs-lookup"><span data-stu-id="37d6a-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="37d6a-258">Přizpůsobení stránek kurzů</span><span class="sxs-lookup"><span data-stu-id="37d6a-258">Customize the Courses Pages</span></span>

<span data-ttu-id="37d6a-259">Když je vytvořena nová entita kurzu, musí mít vztah k existujícímu oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="37d6a-260">Chcete-li přidat oddělení při vytváření kurzu, základní třída pro vytvořit a upravit obsahuje rozevírací seznam pro výběr oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="37d6a-261">Rozevírací seznam nastaví vlastnost cizího `Course.DepartmentID` klíče (FK).</span><span class="sxs-lookup"><span data-stu-id="37d6a-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="37d6a-262">EF Core `Course.DepartmentID` používá FK `Department` k načtení navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="37d6a-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Vytvořit kurz](update-related-data/_static/ddl.png)

<span data-ttu-id="37d6a-264">Aktualizujte model stránky Vytvořit následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="37d6a-265">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="37d6a-265">The preceding code:</span></span>

* <span data-ttu-id="37d6a-266">Pochází z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="37d6a-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="37d6a-267">Používá `TryUpdateModelAsync` se k [zabránění nadměrnému zaúčtování](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="37d6a-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="37d6a-268">Nahradí `ViewData["DepartmentID"]` (ze `DepartmentNameSL` základní třídy).</span><span class="sxs-lookup"><span data-stu-id="37d6a-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="37d6a-269">`ViewData["DepartmentID"]`je nahrazen silným typem `DepartmentNameSL`.</span><span class="sxs-lookup"><span data-stu-id="37d6a-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="37d6a-270">Modely silného typu jsou upřednostňovány před slabě nadanými.</span><span class="sxs-lookup"><span data-stu-id="37d6a-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="37d6a-271">Další informace naleznete [v tématu Slabě zadaný data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="37d6a-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="37d6a-272">Aktualizace stránky Vytvořit kurzy</span><span class="sxs-lookup"><span data-stu-id="37d6a-272">Update the Courses Create page</span></span>

<span data-ttu-id="37d6a-273">Aktualizovat *stránky/kurzy/Create.cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="37d6a-274">Předchozí značky provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="37d6a-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="37d6a-275">Změní titulek z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="37d6a-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="37d6a-276">Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).</span><span class="sxs-lookup"><span data-stu-id="37d6a-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="37d6a-277">Přidá možnost Vybrat oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="37d6a-278">Tato změna vykreslí "Vybrat oddělení" spíše než první oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="37d6a-279">Přidá ověřovací zprávu, když není vybráno oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="37d6a-280">Stránka Razor používá pomocníka [pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="37d6a-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="37d6a-281">Otestujte stránku Vytvořit.</span><span class="sxs-lookup"><span data-stu-id="37d6a-281">Test the Create page.</span></span> <span data-ttu-id="37d6a-282">Na stránce Vytvořit se zobrazí název oddělení, nikoli ID oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="37d6a-283">Aktualizujte stránku Úpravy kurzů.</span><span class="sxs-lookup"><span data-stu-id="37d6a-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="37d6a-284">Nahraďte kód v *Pages/Courses/Edit.cshtml.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="37d6a-285">Změny jsou podobné těm, které byly provedeny v modelu vytvořit stránku.</span><span class="sxs-lookup"><span data-stu-id="37d6a-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="37d6a-286">V předchozím kódu `PopulateDepartmentsDropDownList` předá v ID oddělení, které vybere oddělení zadané v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="37d6a-287">Aktualizovat *stránky/kurzy/Edit.cshtml* pomocí následujících značek:</span><span class="sxs-lookup"><span data-stu-id="37d6a-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="37d6a-288">Předchozí značky provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="37d6a-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="37d6a-289">Zobrazí ID kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-289">Displays the course ID.</span></span> <span data-ttu-id="37d6a-290">Obecně se nezobrazuje primární klíč (PK) entity.</span><span class="sxs-lookup"><span data-stu-id="37d6a-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="37d6a-291">PKs jsou obvykle bezvýznamné pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="37d6a-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="37d6a-292">V tomto případě pk je číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="37d6a-293">Změní titulek z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="37d6a-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="37d6a-294">Nahradí `"ViewBag.DepartmentID"` (ze `DepartmentNameSL` základní třídy).</span><span class="sxs-lookup"><span data-stu-id="37d6a-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="37d6a-295">Stránka obsahuje skryté pole`<input type="hidden">`( ) pro číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="37d6a-296">Přidání `<label>` pomocníka pro `asp-for="Course.CourseID"` značky nevylučuje potřebu skrytého pole.</span><span class="sxs-lookup"><span data-stu-id="37d6a-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="37d6a-297">`<input type="hidden">`je vyžadováno číslo kurzu, které má být zahrnuto do zaúčtovaných dat, když uživatel klikne na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="37d6a-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="37d6a-298">Otestujte aktualizovaný kód.</span><span class="sxs-lookup"><span data-stu-id="37d6a-298">Test the updated code.</span></span> <span data-ttu-id="37d6a-299">Vytvořte, upravte a odstraňte kurz.</span><span class="sxs-lookup"><span data-stu-id="37d6a-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="37d6a-300">Přidat AsNoTracking do modelů stránek Podrobnosti a Odstranit</span><span class="sxs-lookup"><span data-stu-id="37d6a-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="37d6a-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zlepšit výkon, když sledování není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="37d6a-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="37d6a-302">Přidat `AsNoTracking` do modelu stránky Odstranit a Podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="37d6a-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="37d6a-303">Následující kód zobrazuje aktualizovaný model stránky Delete:</span><span class="sxs-lookup"><span data-stu-id="37d6a-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="37d6a-304">Aktualizujte `OnGetAsync` metodu v souboru *Pages/Courses/Details.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="37d6a-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="37d6a-305">Změna stránek Odstranit a Podrobnosti</span><span class="sxs-lookup"><span data-stu-id="37d6a-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="37d6a-306">Aktualizujte stránku Odstranit holicí strojek pomocí následujících značek:</span><span class="sxs-lookup"><span data-stu-id="37d6a-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="37d6a-307">Proveďte stejné změny na stránce Podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="37d6a-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="37d6a-308">Otestujte stránky kurzu</span><span class="sxs-lookup"><span data-stu-id="37d6a-308">Test the Course pages</span></span>

<span data-ttu-id="37d6a-309">Vyzkoušejte vytvářet, upravovat, detaily a odstraňovat.</span><span class="sxs-lookup"><span data-stu-id="37d6a-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="37d6a-310">Aktualizace stránek instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-310">Update the instructor pages</span></span>

<span data-ttu-id="37d6a-311">V následujících částech aktualizovat stránky instruktora.</span><span class="sxs-lookup"><span data-stu-id="37d6a-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="37d6a-312">Přidání umístění kanceláře</span><span class="sxs-lookup"><span data-stu-id="37d6a-312">Add office location</span></span>

<span data-ttu-id="37d6a-313">Při úpravách záznamu instruktora můžete aktualizovat přiřazení kanceláře instruktora.</span><span class="sxs-lookup"><span data-stu-id="37d6a-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="37d6a-314">Entita `Instructor` má vztah 1:0 nebo jedna `OfficeAssignment` s entitou.</span><span class="sxs-lookup"><span data-stu-id="37d6a-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="37d6a-315">Kód instruktora musí zpracovávat:</span><span class="sxs-lookup"><span data-stu-id="37d6a-315">The instructor code must handle:</span></span>

* <span data-ttu-id="37d6a-316">Pokud uživatel vymaže přiřazení `OfficeAssignment` kanceláře, odstraňte entitu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="37d6a-317">Pokud uživatel zadá přiřazení kanceláře a bude prázdné, vytvořte novou `OfficeAssignment` entitu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="37d6a-318">Pokud uživatel změní přiřazení kanceláře, `OfficeAssignment` aktualizujte entitu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="37d6a-319">Aktualizujte instruktory Upravit model stránky pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="37d6a-320">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="37d6a-320">The preceding code:</span></span>

* <span data-ttu-id="37d6a-321">Získá aktuální `Instructor` entitu z databáze `OfficeAssignment` pomocí eager načítání pro navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="37d6a-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="37d6a-322">Aktualizuje načtenou `Instructor` entitu hodnotami z pořadače modelu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="37d6a-323">`TryUpdateModel`zabraňuje [přeúčtování](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="37d6a-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="37d6a-324">Pokud je umístění kanceláře `Instructor.OfficeAssignment` prázdné, nastaví hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="37d6a-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="37d6a-325">Pokud `Instructor.OfficeAssignment` je null, související `OfficeAssignment` řádek v tabulce je odstraněn.</span><span class="sxs-lookup"><span data-stu-id="37d6a-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="37d6a-326">Aktualizace stránky Úprav instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-326">Update the instructor Edit page</span></span>

<span data-ttu-id="37d6a-327">Aktualizovat *stránky/instruktoři/Edit.cshtml* s umístěním kanceláře:</span><span class="sxs-lookup"><span data-stu-id="37d6a-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="37d6a-328">Ověřte, zda můžete změnit umístění instruktoři kanceláře.</span><span class="sxs-lookup"><span data-stu-id="37d6a-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="37d6a-329">Přidání úkolů kurzu na stránku Úpravy instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="37d6a-330">Instruktoři mohou vyučovat libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="37d6a-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="37d6a-331">V této části přidáte možnost změnit přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="37d6a-332">Následující obrázek znázorňuje aktualizovanou stránku úprav instruktora:</span><span class="sxs-lookup"><span data-stu-id="37d6a-332">The following image shows the updated instructor Edit page:</span></span>

![Stránka úprav instruktora s kurzy](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="37d6a-334">`Course`a `Instructor` má vztah n:N.</span><span class="sxs-lookup"><span data-stu-id="37d6a-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="37d6a-335">Chcete-li přidat a odebrat vztahy, `CourseAssignments` přidejte a odeberte entity ze sady entit spojení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="37d6a-336">Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="37d6a-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="37d6a-337">Pro každý kurz v databázi se zobrazí zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="37d6a-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="37d6a-338">Kurzy, ke kterým je instruktor přiřazen, jsou kontrolovány.</span><span class="sxs-lookup"><span data-stu-id="37d6a-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="37d6a-339">Uživatel může zaškrtnout nebo zrušit zaškrtnutí políček pro změnu přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="37d6a-340">Pokud by počet kurzů byl mnohem větší:</span><span class="sxs-lookup"><span data-stu-id="37d6a-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="37d6a-341">Pravděpodobně byste k zobrazení kurzů použili jiné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="37d6a-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="37d6a-342">Způsob manipulace s entitou spojení za účelem vytvoření nebo odstranění relací by se nezměnil.</span><span class="sxs-lookup"><span data-stu-id="37d6a-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="37d6a-343">Přidání kurzů pro podporu vytváření a úprav stránek instruktora</span><span class="sxs-lookup"><span data-stu-id="37d6a-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="37d6a-344">Vytvořte *SchoolViewModels/AssignedCourseData.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="37d6a-345">Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro přiřazené kurzy instruktorem.</span><span class="sxs-lookup"><span data-stu-id="37d6a-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="37d6a-346">Vytvořte základní třídu *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs:*</span><span class="sxs-lookup"><span data-stu-id="37d6a-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="37d6a-347">Jedná `InstructorCoursesPageModel` se o základní třídu, kterou použijete pro modely stránek Úpravy a Vytváření.</span><span class="sxs-lookup"><span data-stu-id="37d6a-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="37d6a-348">`PopulateAssignedCourseData`přečte `Course` všechny entity `AssignedCourseDataList`k naplnění .</span><span class="sxs-lookup"><span data-stu-id="37d6a-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="37d6a-349">Pro každý kurz kód `CourseID`nastaví , název a zda je instruktor přiřazen ke kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="37d6a-350">Sada [hashset](/dotnet/api/system.collections.generic.hashset-1) se používá k vytvoření efektivní vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="37d6a-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="37d6a-351">Instruktoři Upravit model stránky</span><span class="sxs-lookup"><span data-stu-id="37d6a-351">Instructors Edit page model</span></span>

<span data-ttu-id="37d6a-352">Aktualizujte model stránky pro úpravy instruktora pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="37d6a-353">Předchozí kód zpracovává změny přiřazení kanceláře.</span><span class="sxs-lookup"><span data-stu-id="37d6a-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="37d6a-354">Aktualizace zobrazení instruktora Razor:</span><span class="sxs-lookup"><span data-stu-id="37d6a-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="37d6a-355">Při vložení kódu v sadě Visual Studio se konce řádků změní způsobem, který přeruší kód.</span><span class="sxs-lookup"><span data-stu-id="37d6a-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="37d6a-356">Jedním stisknutím kombinace kláves Ctrl+Z můžete automatické formátování vrátit zpět.</span><span class="sxs-lookup"><span data-stu-id="37d6a-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="37d6a-357">Ctrl+Z opravuje konce řádků tak, aby vypadaly tak, jak vidíte zde.</span><span class="sxs-lookup"><span data-stu-id="37d6a-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="37d6a-358">Odsazení nemusí být dokonalé, ale `@:</tr><tr>` `@:<td>`, `@:</td>`, `@:</tr>` a řádky musí být na jednom řádku, jak je znázorněno.</span><span class="sxs-lookup"><span data-stu-id="37d6a-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="37d6a-359">Pokud je vybrán blok nového kódu, třikrát stiskněte klávesu Tab, abyste nový kód zařazovali s existujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="37d6a-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="37d6a-360">Hlasujte o stavu této chyby nebo jej zkontrolujte [pomocí tohoto odkazu](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="37d6a-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="37d6a-361">Předchozí kód vytvoří tabulku HTML, která má tři sloupce.</span><span class="sxs-lookup"><span data-stu-id="37d6a-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="37d6a-362">Každý sloupec má zaškrtávací políčko a titulek obsahující číslo kurzu a název.</span><span class="sxs-lookup"><span data-stu-id="37d6a-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="37d6a-363">Všechna zaškrtávací políčka mají stejný název ("vybrané kurzy").</span><span class="sxs-lookup"><span data-stu-id="37d6a-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="37d6a-364">Použití stejného názvu informuje pořadač modelu, aby s nimi zacházel jako se skupinou.</span><span class="sxs-lookup"><span data-stu-id="37d6a-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="37d6a-365">Atribut hodnoty každého zaškrtávacího políčka je nastaven na hodnotu `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="37d6a-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="37d6a-366">Po zaúčtování stránky předává pořadač modelu pole, `CourseID` které se skládá z hodnot pouze pro vybraná zaškrtávací políčka.</span><span class="sxs-lookup"><span data-stu-id="37d6a-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="37d6a-367">Při počátečním vykreslení zaškrtávacích políček byly kurzy přiřazené instruktorovi zkontrolovány atributy.</span><span class="sxs-lookup"><span data-stu-id="37d6a-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="37d6a-368">Spusťte aplikaci a otestujte stránku pro úpravy aktualizovaných instruktorů.</span><span class="sxs-lookup"><span data-stu-id="37d6a-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="37d6a-369">Změňte některé úkoly kurzu.</span><span class="sxs-lookup"><span data-stu-id="37d6a-369">Change some course assignments.</span></span> <span data-ttu-id="37d6a-370">Změny se projeví na stránce Index.</span><span class="sxs-lookup"><span data-stu-id="37d6a-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="37d6a-371">Poznámka: Přístup, který zde byl přijat k úpravě dat instruktorského kurzu, funguje dobře, když je omezený počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="37d6a-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="37d6a-372">Pro kolekce, které jsou mnohem větší, jiné ui a různé metody aktualizace by bylo více použitelné a efektivní.</span><span class="sxs-lookup"><span data-stu-id="37d6a-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="37d6a-373">Aktualizace stránky Vytvořit instruktory</span><span class="sxs-lookup"><span data-stu-id="37d6a-373">Update the instructors Create page</span></span>

<span data-ttu-id="37d6a-374">Aktualizace instruktora Vytvořit model stránky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="37d6a-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="37d6a-375">Předchozí kód je podobný kódu *Pages/Instructors/Edit.cshtml.cs.*</span><span class="sxs-lookup"><span data-stu-id="37d6a-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="37d6a-376">Aktualizujte stránku Vytvoření břitvy instruktora pomocí následujících značek:</span><span class="sxs-lookup"><span data-stu-id="37d6a-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="37d6a-377">Otestujte stránku Vytvoření instruktora.</span><span class="sxs-lookup"><span data-stu-id="37d6a-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="37d6a-378">Aktualizace stránky Odstranit</span><span class="sxs-lookup"><span data-stu-id="37d6a-378">Update the Delete page</span></span>

<span data-ttu-id="37d6a-379">Aktualizujte model stránky Odstranit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="37d6a-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="37d6a-380">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="37d6a-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="37d6a-381">Používá eager načítání `CourseAssignments` pro navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="37d6a-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="37d6a-382">`CourseAssignments`musí být zahrnuty, nebo nejsou odstraněny, když je instruktor odstraněn.</span><span class="sxs-lookup"><span data-stu-id="37d6a-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="37d6a-383">Chcete-li se vyhnout nutnosti jejich čtení, nakonfigurujte kaskádové odstranění v databázi.</span><span class="sxs-lookup"><span data-stu-id="37d6a-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="37d6a-384">Pokud je instruktor, který má být odstraněn, přiřazen jako správce všech oddělení, odebere přiřazení instruktora z těchto oddělení.</span><span class="sxs-lookup"><span data-stu-id="37d6a-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37d6a-385">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="37d6a-385">Additional resources</span></span>

* [<span data-ttu-id="37d6a-386">YouTube verze tohoto výukového programu (část 1)</span><span class="sxs-lookup"><span data-stu-id="37d6a-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="37d6a-387">YouTube verze tohoto výukového programu (část 2)</span><span class="sxs-lookup"><span data-stu-id="37d6a-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="37d6a-388">[Předchozí](xref:data/ef-rp/read-related-data)
> [další](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="37d6a-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
