---
title: Razor Pages s EF Core ve službě ASP.NET Core – data související s aktualizací – 7 z 8
author: rick-anderson
description: V tomto kurzu aktualizujete související data aktualizací polí cizího klíče a vlastností navigace.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: fdfdb14ff8414b8bf30f9b95be7ba0a6bcbd2995
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656420"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="f5071-103">Razor Pages s EF Core ve službě ASP.NET Core – data související s aktualizací – 7 z 8</span><span class="sxs-lookup"><span data-stu-id="f5071-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="f5071-104">[Dykstra](https://github.com/tdykstra)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f5071-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5071-105">V tomto kurzu se dozvíte, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="f5071-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="f5071-106">Následující ilustrace znázorňují některé z dokončených stránek.</span><span class="sxs-lookup"><span data-stu-id="f5071-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="f5071-107">Stránka pro úpravy kurzu ![](update-related-data/_static/course-edit30.png)
![stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="f5071-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="f5071-108">Aktualizovat stránky pro vytvoření a úpravu kurzu</span><span class="sxs-lookup"><span data-stu-id="f5071-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="f5071-109">Vygenerovaný kód pro stránky pro vytvoření a úpravu kurzu má rozevírací seznam oddělení, který zobrazuje ID oddělení (celé číslo).</span><span class="sxs-lookup"><span data-stu-id="f5071-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="f5071-110">Rozevírací seznam by měl zobrazovat název oddělení, takže obě tyto stránky budou potřebovat seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="f5071-111">Chcete-li poskytnout tento seznam, použijte pro stránky vytvořit a upravit základní třídu.</span><span class="sxs-lookup"><span data-stu-id="f5071-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="f5071-112">Vytvoří základní třídu pro vytvoření a úpravu kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="f5071-113">Vytvořte soubor *Pages/kurzys/DepartmentNamePageModel. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="f5071-114">Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , který bude obsahovat seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="f5071-115">Je-li zadán `selectedDepartment`, je toto oddělení vybráno v `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="f5071-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="f5071-116">Třídy modelu stránky pro vytváření a úpravy budou odvozeny z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="f5071-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="f5071-117">Aktualizace modelu stránky vytvoření kurzu</span><span class="sxs-lookup"><span data-stu-id="f5071-117">Update the Course Create page model</span></span>

<span data-ttu-id="f5071-118">Do oddělení se přiřadí kurz.</span><span class="sxs-lookup"><span data-stu-id="f5071-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="f5071-119">Základní třída pro stránky pro vytváření a úpravy poskytuje `SelectList` pro výběr oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="f5071-120">Rozevírací seznam, který používá `SelectList` nastaví vlastnost `Course.DepartmentID` cizího klíče (FK).</span><span class="sxs-lookup"><span data-stu-id="f5071-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="f5071-121">EF Core používá `Course.DepartmentID` FK k načtení vlastnosti `Department` navigace.</span><span class="sxs-lookup"><span data-stu-id="f5071-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Vytvořit kurz](update-related-data/_static/ddl30.png)

<span data-ttu-id="f5071-123">Aktualizovat *stránky/kurzy/vytvořit. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f5071-124">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="f5071-124">The preceding code:</span></span>

* <span data-ttu-id="f5071-125">Je odvozen z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="f5071-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="f5071-126">Nástroj používá `TryUpdateModelAsync` k zabránění [přestavení](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="f5071-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5071-127">Odebere `ViewData["DepartmentID"]`.</span><span class="sxs-lookup"><span data-stu-id="f5071-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="f5071-128">`DepartmentNameSL` ze základní třídy je model silného typu a bude použit stránkou Razor.</span><span class="sxs-lookup"><span data-stu-id="f5071-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="f5071-129">Modely silného typu jsou upřednostňovány přes slabě zadaná.</span><span class="sxs-lookup"><span data-stu-id="f5071-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="f5071-130">Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="f5071-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="f5071-131">Aktualizace stránky s vytvořením kurzu – vytvořit Razor</span><span class="sxs-lookup"><span data-stu-id="f5071-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="f5071-132">Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="f5071-133">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f5071-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5071-134">Změní titulek z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="f5071-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5071-135">Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="f5071-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="f5071-136">Přidá možnost vybrat oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="f5071-137">Tato změna vykreslí "vybrat oddělení" v rozevíracím seznamu, pokud zatím nebylo vybráno žádné oddělení, nikoli první oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="f5071-138">Přidá ověřovací zprávu, když není vybrané oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="f5071-139">Stránka Razor používá [Pomocníka pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="f5071-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="f5071-140">Otestujte stránku vytvořit.</span><span class="sxs-lookup"><span data-stu-id="f5071-140">Test the Create page.</span></span> <span data-ttu-id="f5071-141">Na stránce vytvořit se zobrazuje název oddělení, nikoli ID oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="f5071-142">Aktualizace modelu stránky pro úpravu kurzu</span><span class="sxs-lookup"><span data-stu-id="f5071-142">Update the Course Edit page model</span></span>

<span data-ttu-id="f5071-143">Aktualizovat *stránky/kurzy/upravit. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="f5071-144">Změny jsou podobné těm, které byly provedeny v modelu vytvoření stránky.</span><span class="sxs-lookup"><span data-stu-id="f5071-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="f5071-145">V předchozím kódu `PopulateDepartmentsDropDownList` předá do ID oddělení, které v rozevíracím seznamu vybere toto oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="f5071-146">Aktualizace stránky kurz úpravy Razor</span><span class="sxs-lookup"><span data-stu-id="f5071-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="f5071-147">Aktualizovat *stránky/kurzy/upravit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="f5071-148">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f5071-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5071-149">Zobrazí ID kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-149">Displays the course ID.</span></span> <span data-ttu-id="f5071-150">Obecně se nezobrazuje primární klíč (PK) entity.</span><span class="sxs-lookup"><span data-stu-id="f5071-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="f5071-151">PKs mají pro uživatele obvykle význam.</span><span class="sxs-lookup"><span data-stu-id="f5071-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="f5071-152">V tomto případě je PK číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="f5071-153">Změní titulek rozevíracího seznamu oddělení z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="f5071-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5071-154">Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="f5071-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="f5071-155">Stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="f5071-156">Přidání pomocné rutiny značky `<label>` s `asp-for="Course.CourseID"` eliminuje nutnost skrytého pole.</span><span class="sxs-lookup"><span data-stu-id="f5071-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="f5071-157">`<input type="hidden">` se vyžaduje, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f5071-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="f5071-158">Aktualizujte podrobnosti kurzu a odstraňte stránky.</span><span class="sxs-lookup"><span data-stu-id="f5071-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="f5071-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="f5071-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="f5071-160">Aktualizace modelů stránek kurzu</span><span class="sxs-lookup"><span data-stu-id="f5071-160">Update the Course page models</span></span>

<span data-ttu-id="f5071-161">Aktualizujte *stránky/kurzy/odstraňte. cshtml. cs* s následujícím kódem pro přidání `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="f5071-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="f5071-162">Udělejte stejnou změnu v souboru *Pages/kurzy/details. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="f5071-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="f5071-163">Aktualizace stránek kurzu Razor</span><span class="sxs-lookup"><span data-stu-id="f5071-163">Update the Course Razor pages</span></span>

<span data-ttu-id="f5071-164">Aktualizovat *stránky/kurzy/odstranit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="f5071-165">Udělejte stejné změny na stránce s podrobnostmi.</span><span class="sxs-lookup"><span data-stu-id="f5071-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="f5071-166">Testování stránek kurzu</span><span class="sxs-lookup"><span data-stu-id="f5071-166">Test the Course pages</span></span>

<span data-ttu-id="f5071-167">Otestujte stránky vytvořit, upravit, podrobnosti a odstranit.</span><span class="sxs-lookup"><span data-stu-id="f5071-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="f5071-168">Aktualizace stránek a vytváření a úpravy instruktora</span><span class="sxs-lookup"><span data-stu-id="f5071-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="f5071-169">Instruktoři můžou učit libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="f5071-170">Na následujícím obrázku vidíte stránku pro úpravu instruktora s poli pro pole kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="f5071-172">Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="f5071-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="f5071-173">Pro každý kurz v databázi se zobrazí zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="f5071-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="f5071-174">Jsou vybrány kurzy, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="f5071-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="f5071-175">Uživatel může zaškrtnout nebo zrušit zaškrtnutí políček pro změnu přiřazení kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="f5071-176">Pokud byl počet kurzů mnohem větší, může být lepší pracovat s jiným uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="f5071-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="f5071-177">Nicméně, metoda správy vztahu n:n se tady nemění.</span><span class="sxs-lookup"><span data-stu-id="f5071-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="f5071-178">Chcete-li vytvořit nebo odstranit relace, budete pracovat s entitou JOIN.</span><span class="sxs-lookup"><span data-stu-id="f5071-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="f5071-179">Vytvoření třídy pro přiřazená data kurzů</span><span class="sxs-lookup"><span data-stu-id="f5071-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="f5071-180">Vytvořte *SchoolViewModels/AssignedCourseData. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="f5071-181">Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro kurzy přiřazené instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="f5071-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="f5071-182">Vytvoření základní třídy modelu stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="f5071-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="f5071-183">Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cs* :</span><span class="sxs-lookup"><span data-stu-id="f5071-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="f5071-184">`InstructorCoursesPageModel` je základní třída, kterou použijete pro modely stránek upravit a vytvořit.</span><span class="sxs-lookup"><span data-stu-id="f5071-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="f5071-185">`PopulateAssignedCourseData` přečte všechny `Course` entity k naplnění `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="f5071-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="f5071-186">Pro každý kurz kód nastaví `CourseID`, název a bez ohledu na to, jestli je instruktor přiřazený k kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="f5071-187">[HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá pro efektivní vyhledávání.</span><span class="sxs-lookup"><span data-stu-id="f5071-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="f5071-188">Vzhledem k tomu, že stránka Razor nemá kolekci entit kurzu, nemůže pořadač modelů automaticky aktualizovat `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f5071-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f5071-189">Namísto použití pořadače modelů k aktualizaci `CourseAssignments` navigační vlastnosti to uděláte v nové metodě `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="f5071-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="f5071-190">Proto je nutné vyloučit vlastnost `CourseAssignments` z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="f5071-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="f5071-191">To nevyžaduje žádné změny kódu, který volá `TryUpdateModel`, protože používáte přetížení seznamu povolených položek a `CourseAssignments` není v seznamu zahrnutí.</span><span class="sxs-lookup"><span data-stu-id="f5071-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="f5071-192">Pokud nebyla vybrána žádná zaškrtávací políčka, kód v `UpdateInstructorCourses` inicializuje vlastnost `CourseAssignments` navigace s prázdnou kolekcí a vrátí:</span><span class="sxs-lookup"><span data-stu-id="f5071-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="f5071-193">Kód pak projde všemi kurzy v databázi a zkontroluje každý kurz s těmi, které jsou aktuálně přiřazeny k instruktorovi, oproti těm, které byly vybrány na stránce.</span><span class="sxs-lookup"><span data-stu-id="f5071-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="f5071-194">Aby bylo možné efektivně vyhledávat, jsou tyto dvě kolekce uloženy v `HashSet`ch objektech.</span><span class="sxs-lookup"><span data-stu-id="f5071-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="f5071-195">Pokud je vybráno zaškrtávací políčko pro kurz, ale kurz není v navigační vlastnosti `Instructor.CourseAssignments`, kurz se přidá do kolekce v navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f5071-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="f5071-196">Pokud není vybrané zaškrtávací políčko pro kurz, ale kurz je v navigační vlastnosti `Instructor.CourseAssignments`, kurz se odebere z navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="f5071-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="f5071-197">Obsluha umístění kanceláře</span><span class="sxs-lookup"><span data-stu-id="f5071-197">Handle office location</span></span>

<span data-ttu-id="f5071-198">Další relaci, kterou musí stránka pro úpravy zpracovat, je relace 1:1, kterou má entita instruktora s entitou `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="f5071-199">Kód pro úpravy instruktora musí zpracovat následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="f5071-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="f5071-200">Pokud uživatel zruší přiřazení sady Office, odstraňte entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5071-201">Pokud uživatel zadá přiřazení pro Office a byl prázdný, vytvořte novou entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5071-202">Pokud uživatel změní přiřazení sady Office, aktualizujte entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="f5071-203">Aktualizace modelu stránky pro úpravu instruktorů</span><span class="sxs-lookup"><span data-stu-id="f5071-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="f5071-204">Aktualizovat *stránky/instruktory/upravit. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="f5071-205">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="f5071-205">The preceding code:</span></span>

* <span data-ttu-id="f5071-206">Získá aktuální entitu `Instructor` z databáze pomocí Eager načítání pro vlastnosti `OfficeAssignment`, `CourseAssignment``CourseAssignment.Course` a navigace.</span><span class="sxs-lookup"><span data-stu-id="f5071-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="f5071-207">Aktualizuje načtenou entitu `Instructor` hodnotami z pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="f5071-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="f5071-208">`TryUpdateModel` brání [přestavení](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="f5071-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5071-209">Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f5071-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="f5071-210">Pokud je `Instructor.OfficeAssignment` null, související řádek v tabulce `OfficeAssignment` se odstraní.</span><span class="sxs-lookup"><span data-stu-id="f5071-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="f5071-211">Volá `PopulateAssignedCourseData` v `OnGetAsync` k poskytnutí informací pro zaškrtávací políčka pomocí třídy modelu zobrazení `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="f5071-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="f5071-212">Volá `UpdateInstructorCourses` v `OnPostAsync` pro použití informací z zaškrtávacích políček na upravované entitě instruktor.</span><span class="sxs-lookup"><span data-stu-id="f5071-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="f5071-213">Pokud `TryUpdateModel` selžou, volání `PopulateAssignedCourseData` a `UpdateInstructorCourses` v `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="f5071-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="f5071-214">Tato volání metody obnoví přiřazená data kurzu zadaná na stránce, když se znovu zobrazí s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="f5071-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="f5071-215">Aktualizace stránky instruktor Edit Razor</span><span class="sxs-lookup"><span data-stu-id="f5071-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="f5071-216">Aktualizovat *stránky/instruktory/upravit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="f5071-217">Předchozí kód vytvoří tabulku HTML, která má tři sloupce.</span><span class="sxs-lookup"><span data-stu-id="f5071-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="f5071-218">Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="f5071-219">Všechna zaškrtávací políčka mají stejný název ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="f5071-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="f5071-220">Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu.</span><span class="sxs-lookup"><span data-stu-id="f5071-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="f5071-221">Atribut Value každé zaškrtávací políčko je nastaven na `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="f5071-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="f5071-222">Po zveřejnění stránky předává pořadač modelu pole, které obsahuje `CourseID` hodnoty pouze pro zaškrtávací políčka, která jsou vybrána.</span><span class="sxs-lookup"><span data-stu-id="f5071-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="f5071-223">Při počátečním vykreslení zaškrtávacích políček se vybere kurz přiřazený instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="f5071-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="f5071-224">Poznámka: přístup, který je zde povedený pro úpravu dat kurzu instruktora funguje dobře, pokud existuje omezený počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="f5071-225">Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="f5071-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="f5071-226">Spusťte aplikaci a otestujte stránku pro úpravu aktualizovaných instruktorů.</span><span class="sxs-lookup"><span data-stu-id="f5071-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="f5071-227">Změna některých přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-227">Change some course assignments.</span></span> <span data-ttu-id="f5071-228">Změny se projeví na stránce indexu.</span><span class="sxs-lookup"><span data-stu-id="f5071-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="f5071-229">Aktualizace stránky pro vytvoření instruktora</span><span class="sxs-lookup"><span data-stu-id="f5071-229">Update the Instructor Create page</span></span>

<span data-ttu-id="f5071-230">Aktualizujte model stránky pro vytvoření a stránku Razor s podobným kódem jako na stránce pro úpravy:</span><span class="sxs-lookup"><span data-stu-id="f5071-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="f5071-231">Otestujte stránku pro vytvoření instruktoru.</span><span class="sxs-lookup"><span data-stu-id="f5071-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="f5071-232">Aktualizace stránky pro odstranění instruktorů</span><span class="sxs-lookup"><span data-stu-id="f5071-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="f5071-233">Aktualizujte *stránky/instruktory/odstraňte. cshtml. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="f5071-234">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f5071-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5071-235">Používá načítání Eager pro navigační vlastnost `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="f5071-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f5071-236">`CourseAssignments` musí být zahrnuté, nebo nejsou po odstranění instruktora smazány.</span><span class="sxs-lookup"><span data-stu-id="f5071-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="f5071-237">Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="f5071-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="f5071-238">Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.</span><span class="sxs-lookup"><span data-stu-id="f5071-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="f5071-239">Spusťte aplikaci a otestujte stránku odstranit.</span><span class="sxs-lookup"><span data-stu-id="f5071-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f5071-240">Další kroky</span><span class="sxs-lookup"><span data-stu-id="f5071-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f5071-241">[Předchozí kurz](xref:data/ef-rp/read-related-data)
> [Další kurz](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="f5071-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5071-242">Tento kurz ukazuje, jak aktualizovat související data.</span><span class="sxs-lookup"><span data-stu-id="f5071-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="f5071-243">Pokud narazíte na problémy, které nemůžete vyřešit, [Stáhněte nebo zobrazte dokončenou aplikaci.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="f5071-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="f5071-244">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f5071-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f5071-245">Na následujících obrázcích vidíte některé z dokončených stránek.</span><span class="sxs-lookup"><span data-stu-id="f5071-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="f5071-246">Stránka pro úpravy kurzu ![](update-related-data/_static/course-edit.png)
![stránka pro úpravy instruktora](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="f5071-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="f5071-247">Projděte a otestujte stránky pro vytvoření a úpravu kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="f5071-248">Vytvořte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="f5071-248">Create a new course.</span></span> <span data-ttu-id="f5071-249">Oddělení je vybráno podle jeho primárního klíče (celé číslo), nikoli podle názvu.</span><span class="sxs-lookup"><span data-stu-id="f5071-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="f5071-250">Upravte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="f5071-250">Edit the new course.</span></span> <span data-ttu-id="f5071-251">Po dokončení testování odstraňte nový kurz.</span><span class="sxs-lookup"><span data-stu-id="f5071-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="f5071-252">Vytvoření základní třídy pro sdílení společného kódu</span><span class="sxs-lookup"><span data-stu-id="f5071-252">Create a base class to share common code</span></span>

<span data-ttu-id="f5071-253">Každý z nich potřebuje seznam názvů oddělení, kurzy a vytváření a kurzy a stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="f5071-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="f5071-254">Vytvořte základní třídu *Pages/kurzy/DepartmentNamePageModel. cshtml. cs* pro stránky pro vytváření a úpravy:</span><span class="sxs-lookup"><span data-stu-id="f5071-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="f5071-255">Předchozí kód vytvoří [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) , který bude obsahovat seznam názvů oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="f5071-256">Je-li zadán `selectedDepartment`, je toto oddělení vybráno v `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="f5071-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="f5071-257">Třídy modelu stránky pro vytváření a úpravy budou odvozeny z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="f5071-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="f5071-258">Přizpůsobení stránek kurzů</span><span class="sxs-lookup"><span data-stu-id="f5071-258">Customize the Courses Pages</span></span>

<span data-ttu-id="f5071-259">Když se vytvoří nová entita kurzu, musí mít relaci s existujícím oddělením.</span><span class="sxs-lookup"><span data-stu-id="f5071-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="f5071-260">Chcete-li přidat oddělení při vytváření kurzu, základní třída pro vytváření a úpravy obsahuje rozevírací seznam pro výběr oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="f5071-261">Rozevírací seznam nastaví vlastnost `Course.DepartmentID` cizí klíč (FK).</span><span class="sxs-lookup"><span data-stu-id="f5071-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="f5071-262">EF Core používá `Course.DepartmentID` FK k načtení vlastnosti `Department` navigace.</span><span class="sxs-lookup"><span data-stu-id="f5071-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Vytvořit kurz](update-related-data/_static/ddl.png)

<span data-ttu-id="f5071-264">Aktualizujte model vytvoření stránky pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="f5071-265">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="f5071-265">The preceding code:</span></span>

* <span data-ttu-id="f5071-266">Je odvozen z `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="f5071-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="f5071-267">Nástroj používá `TryUpdateModelAsync` k zabránění [přestavení](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="f5071-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5071-268">Nahradí `ViewData["DepartmentID"]` `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="f5071-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="f5071-269">`ViewData["DepartmentID"]` se nahrazuje `DepartmentNameSL`silného typu.</span><span class="sxs-lookup"><span data-stu-id="f5071-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="f5071-270">Modely silného typu jsou upřednostňovány přes slabě zadaná.</span><span class="sxs-lookup"><span data-stu-id="f5071-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="f5071-271">Další informace najdete v tématu [slabě zadaná data (ViewData a ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="f5071-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="f5071-272">Aktualizace stránky pro vytvoření kurzů</span><span class="sxs-lookup"><span data-stu-id="f5071-272">Update the Courses Create page</span></span>

<span data-ttu-id="f5071-273">Aktualizovat *stránky/kurzy/vytvořit. cshtml* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="f5071-274">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f5071-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="f5071-275">Změní titulek z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="f5071-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5071-276">Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="f5071-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="f5071-277">Přidá možnost vybrat oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="f5071-278">Tato změna vykreslí místo prvního oddělení možnost "vybrat oddělení".</span><span class="sxs-lookup"><span data-stu-id="f5071-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="f5071-279">Přidá ověřovací zprávu, když není vybrané oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="f5071-280">Stránka Razor používá [Pomocníka pro výběr značky](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="f5071-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="f5071-281">Otestujte stránku vytvořit.</span><span class="sxs-lookup"><span data-stu-id="f5071-281">Test the Create page.</span></span> <span data-ttu-id="f5071-282">Na stránce vytvořit se zobrazuje název oddělení, nikoli ID oddělení.</span><span class="sxs-lookup"><span data-stu-id="f5071-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="f5071-283">Aktualizujte stránku pro úpravu kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="f5071-284">Nahraďte kód na *stránce/kurzy/upravte. cshtml. cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="f5071-285">Změny jsou podobné těm, které byly provedeny v modelu vytvoření stránky.</span><span class="sxs-lookup"><span data-stu-id="f5071-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="f5071-286">V předchozím kódu `PopulateDepartmentsDropDownList` předá do ID oddělení, které vybere oddělení určené v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="f5071-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="f5071-287">Aktualizovat *stránky/kurzy/upravit. cshtml* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="f5071-288">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f5071-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="f5071-289">Zobrazí ID kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-289">Displays the course ID.</span></span> <span data-ttu-id="f5071-290">Obecně se nezobrazuje primární klíč (PK) entity.</span><span class="sxs-lookup"><span data-stu-id="f5071-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="f5071-291">PKs mají pro uživatele obvykle význam.</span><span class="sxs-lookup"><span data-stu-id="f5071-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="f5071-292">V tomto případě je PK číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="f5071-293">Změní titulek z **DepartmentID** na **oddělení**.</span><span class="sxs-lookup"><span data-stu-id="f5071-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="f5071-294">Nahradí `"ViewBag.DepartmentID"` `DepartmentNameSL` (ze základní třídy).</span><span class="sxs-lookup"><span data-stu-id="f5071-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="f5071-295">Stránka obsahuje skryté pole (`<input type="hidden">`) pro číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="f5071-296">Přidání pomocné rutiny značky `<label>` s `asp-for="Course.CourseID"` eliminuje nutnost skrytého pole.</span><span class="sxs-lookup"><span data-stu-id="f5071-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="f5071-297">`<input type="hidden">` se vyžaduje, aby číslo kurzu bylo zahrnuto do publikovaných dat, když uživatel klikne na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="f5071-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="f5071-298">Otestujte aktualizovaný kód.</span><span class="sxs-lookup"><span data-stu-id="f5071-298">Test the updated code.</span></span> <span data-ttu-id="f5071-299">Vytvořit, upravit a odstranit kurz.</span><span class="sxs-lookup"><span data-stu-id="f5071-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="f5071-300">Přidání AsNoTracking k podrobnostem a odstranění modelů stránek</span><span class="sxs-lookup"><span data-stu-id="f5071-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="f5071-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) může zvýšit výkon, když sledování není vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="f5071-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="f5071-302">Přidejte `AsNoTracking` k modelu stránky odstranění a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="f5071-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="f5071-303">Následující kód ukazuje aktualizovaný model stránky pro odstranění:</span><span class="sxs-lookup"><span data-stu-id="f5071-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="f5071-304">Aktualizujte metodu `OnGetAsync` v souboru *Pages/kurzy/details. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="f5071-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="f5071-305">Úprava stránek odstranění a podrobností</span><span class="sxs-lookup"><span data-stu-id="f5071-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="f5071-306">Aktualizujte stránku odstranění Razor pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="f5071-307">Udělejte stejné změny na stránce s podrobnostmi.</span><span class="sxs-lookup"><span data-stu-id="f5071-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="f5071-308">Testování stránek kurzu</span><span class="sxs-lookup"><span data-stu-id="f5071-308">Test the Course pages</span></span>

<span data-ttu-id="f5071-309">Test vytvořit, upravit, podrobnosti a odstranit.</span><span class="sxs-lookup"><span data-stu-id="f5071-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="f5071-310">Aktualizovat stránky instruktora</span><span class="sxs-lookup"><span data-stu-id="f5071-310">Update the instructor pages</span></span>

<span data-ttu-id="f5071-311">V následujících částech se aktualizují stránky instruktora.</span><span class="sxs-lookup"><span data-stu-id="f5071-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="f5071-312">Přidat umístění kanceláře</span><span class="sxs-lookup"><span data-stu-id="f5071-312">Add office location</span></span>

<span data-ttu-id="f5071-313">Při úpravách záznamu instruktora můžete chtít aktualizovat přiřazení kanceláře instruktora.</span><span class="sxs-lookup"><span data-stu-id="f5071-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="f5071-314">Entita `Instructor` má relaci typu 1:1 s entitou `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="f5071-315">Kód instruktora musí zpracovat:</span><span class="sxs-lookup"><span data-stu-id="f5071-315">The instructor code must handle:</span></span>

* <span data-ttu-id="f5071-316">Pokud uživatel zruší přiřazení sady Office, odstraňte entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5071-317">Pokud uživatel zadá přiřazení pro Office a byl prázdný, vytvořte novou entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="f5071-318">Pokud uživatel změní přiřazení sady Office, aktualizujte entitu `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="f5071-319">Aktualizujte model stránky pro instruktory pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="f5071-320">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="f5071-320">The preceding code:</span></span>

* <span data-ttu-id="f5071-321">Načte aktuální entitu `Instructor` z databáze pomocí načítání Eager pro navigační vlastnost `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="f5071-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="f5071-322">Aktualizuje načtenou entitu `Instructor` hodnotami z pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="f5071-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="f5071-323">`TryUpdateModel` brání [přestavení](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="f5071-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="f5071-324">Pokud je umístění kanceláře prázdné, nastaví `Instructor.OfficeAssignment` na hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f5071-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="f5071-325">Pokud je `Instructor.OfficeAssignment` null, související řádek v tabulce `OfficeAssignment` se odstraní.</span><span class="sxs-lookup"><span data-stu-id="f5071-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="f5071-326">Aktualizace stránky pro úpravu instruktorů</span><span class="sxs-lookup"><span data-stu-id="f5071-326">Update the instructor Edit page</span></span>

<span data-ttu-id="f5071-327">Aktualizovat *stránky/instruktory/upravit. cshtml* s umístěním kanceláře:</span><span class="sxs-lookup"><span data-stu-id="f5071-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="f5071-328">Ověřte, že je možné změnit umístění kanceláře instruktorů.</span><span class="sxs-lookup"><span data-stu-id="f5071-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="f5071-329">Přidání přiřazení kurzů do stránky pro úpravu instruktorů</span><span class="sxs-lookup"><span data-stu-id="f5071-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="f5071-330">Instruktoři můžou učit libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="f5071-331">V této části přidáte možnost změnit přiřazení kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="f5071-332">Na následujícím obrázku je vidět aktualizovaná stránka pro úpravu instruktorů:</span><span class="sxs-lookup"><span data-stu-id="f5071-332">The following image shows the updated instructor Edit page:</span></span>

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="f5071-334">`Course` a `Instructor` má relaci m:n.</span><span class="sxs-lookup"><span data-stu-id="f5071-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="f5071-335">Chcete-li přidat a odebrat relace, přidejte a odeberte entity ze sady entit služby `CourseAssignments` JOIN.</span><span class="sxs-lookup"><span data-stu-id="f5071-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="f5071-336">Zaškrtávací políčka umožňují změny kurzů, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="f5071-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="f5071-337">Pro každý kurz v databázi se zobrazí zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="f5071-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="f5071-338">Budou zkontrolovány kurzy, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="f5071-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="f5071-339">Uživatel může zaškrtnutím nebo zrušením zaškrtnutí políček změnit přiřazení kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="f5071-340">Pokud byl počet kurzů mnohem větší:</span><span class="sxs-lookup"><span data-stu-id="f5071-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="f5071-341">Pro zobrazení kurzů byste pravděpodobně použili jiné uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f5071-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="f5071-342">Metoda manipulace s entitou JOIN k vytvoření nebo odstranění relace se nezměnila.</span><span class="sxs-lookup"><span data-stu-id="f5071-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="f5071-343">Přidat třídy pro podporu vytváření a úprav stránek instruktora</span><span class="sxs-lookup"><span data-stu-id="f5071-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="f5071-344">Vytvořte *SchoolViewModels/AssignedCourseData. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="f5071-345">Třída `AssignedCourseData` obsahuje data pro vytvoření zaškrtávacích políček pro přiřazené kurzy od instruktora.</span><span class="sxs-lookup"><span data-stu-id="f5071-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="f5071-346">Vytvořte základní třídu *Pages/instruktors/InstructorCoursesPageModel. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="f5071-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="f5071-347">`InstructorCoursesPageModel` je základní třída, kterou použijete pro modely stránek upravit a vytvořit.</span><span class="sxs-lookup"><span data-stu-id="f5071-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="f5071-348">`PopulateAssignedCourseData` přečte všechny `Course` entity k naplnění `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="f5071-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="f5071-349">Pro každý kurz kód nastaví `CourseID`, název a bez ohledu na to, jestli je instruktor přiřazený k kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="f5071-350">[HashSet –](/dotnet/api/system.collections.generic.hashset-1) se používá k vytváření efektivních hledání.</span><span class="sxs-lookup"><span data-stu-id="f5071-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="f5071-351">Instruktoři – upravit model stránky</span><span class="sxs-lookup"><span data-stu-id="f5071-351">Instructors Edit page model</span></span>

<span data-ttu-id="f5071-352">Aktualizujte model stránky instruktory pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="f5071-353">Předchozí kód zpracovává změny přiřazení Office.</span><span class="sxs-lookup"><span data-stu-id="f5071-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="f5071-354">Aktualizace zobrazení instruktor Razor:</span><span class="sxs-lookup"><span data-stu-id="f5071-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="f5071-355">Při vložení kódu v aplikaci Visual Studio se konce řádků mění způsobem, který kód přerušuje.</span><span class="sxs-lookup"><span data-stu-id="f5071-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="f5071-356">Stisknutím kombinace kláves CTRL + Z pokaždé vraťte automatické formátování zpět.</span><span class="sxs-lookup"><span data-stu-id="f5071-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="f5071-357">CTRL + Z opravuje konce řádků, aby vypadaly jako v tomto příkladu.</span><span class="sxs-lookup"><span data-stu-id="f5071-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="f5071-358">Odsazení nemusí být dokonalé, ale `@:</tr><tr>`, `@:<td>`, `@:</td>`a `@:</tr>` řádky musí být na jednom řádku, jak je znázorněno na obrázku.</span><span class="sxs-lookup"><span data-stu-id="f5071-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="f5071-359">Po vybrání bloku nového kódu stiskněte klávesu Tabulátor třikrát, aby se nový kód pořádek nastavil s existujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="f5071-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="f5071-360">Hlasujte nebo zkontrolujte stav této chyby [pomocí tohoto odkazu](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="f5071-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="f5071-361">Předchozí kód vytvoří tabulku HTML, která má tři sloupce.</span><span class="sxs-lookup"><span data-stu-id="f5071-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="f5071-362">Každý sloupec má zaškrtávací políčko a popisek obsahující číslo a název kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="f5071-363">Všechna zaškrtávací políčka mají stejný název ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="f5071-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="f5071-364">Použití stejného názvu informuje pořadač modelů, aby je považoval za skupinu.</span><span class="sxs-lookup"><span data-stu-id="f5071-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="f5071-365">Atribut Value každé zaškrtávací políčko je nastaven na `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="f5071-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="f5071-366">Po zveřejnění stránky předává pořadač modelu pole, které se skládá z `CourseID` hodnoty pouze pro zaškrtnutá políčka.</span><span class="sxs-lookup"><span data-stu-id="f5071-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="f5071-367">Když jsou zaškrtávací políčka zpočátku vykreslena, kurzy přiřazené instruktorovi mají zaškrtnuté atributy.</span><span class="sxs-lookup"><span data-stu-id="f5071-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="f5071-368">Spusťte aplikaci a otestujte stránku pro úpravu aktualizovaných instruktorů.</span><span class="sxs-lookup"><span data-stu-id="f5071-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="f5071-369">Změna některých přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="f5071-369">Change some course assignments.</span></span> <span data-ttu-id="f5071-370">Změny se projeví na stránce indexu.</span><span class="sxs-lookup"><span data-stu-id="f5071-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="f5071-371">Poznámka: přístup, který je zde povedený pro úpravu dat kurzu instruktora funguje dobře, pokud existuje omezený počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="f5071-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="f5071-372">Pro kolekce, které jsou mnohem větší, jiné uživatelské rozhraní a odlišnou metodu aktualizace by byly efektivnější a efektivnější.</span><span class="sxs-lookup"><span data-stu-id="f5071-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="f5071-373">Aktualizace stránky pro vytvoření instruktorů</span><span class="sxs-lookup"><span data-stu-id="f5071-373">Update the instructors Create page</span></span>

<span data-ttu-id="f5071-374">Aktualizujte model stránky pro vytvoření instruktoru pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="f5071-375">Předchozí kód je podobný jako u kódu *stránky, instruktory/upravit. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="f5071-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="f5071-376">Aktualizujte stránku Instructor Create Razor pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f5071-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="f5071-377">Otestujte stránku pro vytvoření instruktoru.</span><span class="sxs-lookup"><span data-stu-id="f5071-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="f5071-378">Aktualizovat stránku Delete</span><span class="sxs-lookup"><span data-stu-id="f5071-378">Update the Delete page</span></span>

<span data-ttu-id="f5071-379">Aktualizace modelu odstranění stránky s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f5071-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="f5071-380">Předchozí kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="f5071-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f5071-381">Používá načítání Eager pro navigační vlastnost `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="f5071-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="f5071-382">`CourseAssignments` musí být zahrnuté, nebo nejsou po odstranění instruktora smazány.</span><span class="sxs-lookup"><span data-stu-id="f5071-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="f5071-383">Abyste se vyhnuli nutnosti jejich čtení, nakonfigurujte v databázi kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="f5071-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="f5071-384">Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.</span><span class="sxs-lookup"><span data-stu-id="f5071-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5071-385">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f5071-385">Additional resources</span></span>

* [<span data-ttu-id="f5071-386">Verze tohoto kurzu pro YouTube (část 1)</span><span class="sxs-lookup"><span data-stu-id="f5071-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="f5071-387">Verze tohoto kurzu pro YouTube (část 2)</span><span class="sxs-lookup"><span data-stu-id="f5071-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="f5071-388">[Předchozí](xref:data/ef-rp/read-related-data)
> [Další](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="f5071-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
