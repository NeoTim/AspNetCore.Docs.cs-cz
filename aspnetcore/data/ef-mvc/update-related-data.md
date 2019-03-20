---
title: 'Kurz: Aktualizace souvisejících dat – ASP.NET MVC s EF Core'
description: V tomto kurzu budete aktualizovat souvisejících dat prostřednictvím aktualizace pole cizích klíčů a navigační vlastnosti.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 1606b872df2df839266ef17efee1948065c4efae
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209411"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="33c0a-103">Kurz: Aktualizace souvisejících dat – ASP.NET MVC s EF Core</span><span class="sxs-lookup"><span data-stu-id="33c0a-103">Tutorial: Update related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="33c0a-104">V předchozím kurzu zobrazí související data; v tomto kurzu budete aktualizovat souvisejících dat prostřednictvím aktualizace pole cizích klíčů a navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="33c0a-104">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="33c0a-105">Následující ilustrace znázorňují některé stránky, které budete pracovat.</span><span class="sxs-lookup"><span data-stu-id="33c0a-105">The following illustrations show some of the pages that you'll work with.</span></span>

![Kurz stránky pro úpravu](update-related-data/_static/course-edit.png)

![Stránky pro úpravu instruktorem](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="33c0a-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="33c0a-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="33c0a-109">Přizpůsobení stránek kurzy</span><span class="sxs-lookup"><span data-stu-id="33c0a-109">Customize Courses pages</span></span>
> * <span data-ttu-id="33c0a-110">Přidat Instruktoři upravit stránku</span><span class="sxs-lookup"><span data-stu-id="33c0a-110">Add Instructors Edit page</span></span>
> * <span data-ttu-id="33c0a-111">Přidejte do stránky pro úpravu kurzy</span><span class="sxs-lookup"><span data-stu-id="33c0a-111">Add courses to Edit page</span></span>
> * <span data-ttu-id="33c0a-112">Stránka pro aktualizaci Delete</span><span class="sxs-lookup"><span data-stu-id="33c0a-112">Update Delete page</span></span>
> * <span data-ttu-id="33c0a-113">Přidat stránku vytvořit pobočky a kurzy</span><span class="sxs-lookup"><span data-stu-id="33c0a-113">Add office location and courses to Create page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="33c0a-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="33c0a-114">Prerequisites</span></span>

* [<span data-ttu-id="33c0a-115">Čtení souvisejících dat s EF Core pro webovou aplikaci ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="33c0a-115">Read related data with EF Core for an ASP.NET Core MVC web app</span></span>](read-related-data.md)

## <a name="customize-courses-pages"></a><span data-ttu-id="33c0a-116">Přizpůsobení stránek kurzy</span><span class="sxs-lookup"><span data-stu-id="33c0a-116">Customize Courses pages</span></span>

<span data-ttu-id="33c0a-117">Při vytvoření nové entity kurzu musí mít relaci k existující oddělení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-117">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="33c0a-118">K provedení této obsahuje automaticky generovaný kód metody kontroleru a vytvořit a upravit zobrazení, které zahrnují rozevíracího seznamu pro výběr oddělení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-118">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="33c0a-119">Sady rozevíracího seznamu `Course.DepartmentID` vlastnost cizího klíče, a to je všechny Entity Framework, které potřebujete-li načíst `Department` navigační vlastnost s odpovídající entita oddělení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-119">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="33c0a-120">Budete používat automaticky generovaný kód, ale mírně se přidání zpracování chyb a rozevírací seznam seřadit změnit.</span><span class="sxs-lookup"><span data-stu-id="33c0a-120">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="33c0a-121">V *CoursesController.cs*, odstraňte čtyři metody vytvoření a úprava a nahraďte následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="33c0a-121">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="33c0a-122">Po `Edit` metoda HttpPost vytvoření nové metody, který načítá informace o oddělení pro rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="33c0a-123">`PopulateDepartmentsDropDownList` Metoda načte seznam všech oddělení seřazené podle názvu, vytvoří `SelectList` kolekce rozevírací seznam a předá kolekce na zobrazení `ViewBag`.</span><span class="sxs-lookup"><span data-stu-id="33c0a-123">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="33c0a-124">Metoda přijímá volitelný `selectedDepartment` parametr, který umožňuje volajícímu kódu k určení, které se při vykreslení rozevíracího seznamu vybrat položku.</span><span class="sxs-lookup"><span data-stu-id="33c0a-124">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="33c0a-125">Zobrazení předá název "DepartmentID" `<select>` pomocné rutiny značky a pomocné rutiny pak mohl podívejte se `ViewBag` objekt pro `SelectList` s názvem "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="33c0a-125">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="33c0a-126">Třídy MetadataExchangeClientMode `Create` volání metod `PopulateDepartmentsDropDownList` metody bez nastavení vybrané položky, protože pro nový kurz oddělení nedojde k ještě:</span><span class="sxs-lookup"><span data-stu-id="33c0a-126">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="33c0a-127">Třídy MetadataExchangeClientMode `Edit` metoda nastaví vybrané položky na základě ID oddělení, které je již přiřazen ke kurzu, který právě upravujete:</span><span class="sxs-lookup"><span data-stu-id="33c0a-127">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="33c0a-128">Pro obě metody HttpPost `Create` a `Edit` také obsahovat kód, který nastaví vybrané položky při jejich opětovné zobrazení na stránce po chybě.</span><span class="sxs-lookup"><span data-stu-id="33c0a-128">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="33c0a-129">Tím se zajistí, že při stránce se zobrazí znovu, chcete-li zobrazit chybová zpráva, ať oddělení byla vybrána pořád vybraný.</span><span class="sxs-lookup"><span data-stu-id="33c0a-129">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="33c0a-130">Přidáte. AsNoTracking podrobností a metod Delete</span><span class="sxs-lookup"><span data-stu-id="33c0a-130">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="33c0a-131">Chcete-li optimalizovat výkon podrobnosti o kurzu a odstranění stránek, přidejte `AsNoTracking` volání `Details` a HttpGet `Delete` metody.</span><span class="sxs-lookup"><span data-stu-id="33c0a-131">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="33c0a-132">Upravit zobrazení kurzů</span><span class="sxs-lookup"><span data-stu-id="33c0a-132">Modify the Course views</span></span>

<span data-ttu-id="33c0a-133">V *Views/Courses/Create.cshtml*, přidat možnost "Vybrat oddělení" k **oddělení** rozevíracího seznamu, změnit popisek z **DepartmentID** k  **Oddělení**a přidat ověřovací zprávu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-133">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="33c0a-134">V *Views/Courses/Edit.cshtml*, provést stejnou změnu pro pole oddělení, který jste právě provedli v *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33c0a-134">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="33c0a-135">Také v *Views/Courses/Edit.cshtml*, přidejte pole číslo kurzu před **název** pole.</span><span class="sxs-lookup"><span data-stu-id="33c0a-135">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="33c0a-136">Vzhledem k tomu, že číslo kurzu je primárním klíčem, se zobrazí, ale nelze změnit.</span><span class="sxs-lookup"><span data-stu-id="33c0a-136">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="33c0a-137">Již existuje skrytá pole (`<input type="hidden">`) pro číslo kurzu v zobrazení pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="33c0a-137">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="33c0a-138">Přidávání `<label>` pomocné rutiny značky není eliminuje nutnost skryté pole, protože nezpůsobí kurzu číslo, které má být součástí odeslaných dat, když uživatel klikne **Uložit** na **upravit** stránky.</span><span class="sxs-lookup"><span data-stu-id="33c0a-138">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="33c0a-139">V *Views/Courses/Delete.cshtml*kurzu číselného pole v horní části a přidejte změnit ID oddělení název oddělení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-139">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="33c0a-140">V *Views/Courses/Details.cshtml*, provést stejnou změnu, který jste právě provedli pro *Delete.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33c0a-140">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="33c0a-141">Testování stránek kurzu</span><span class="sxs-lookup"><span data-stu-id="33c0a-141">Test the Course pages</span></span>

<span data-ttu-id="33c0a-142">Spusťte aplikaci, vyberte **kurzy** klikněte na tlačítko **vytvořit nový**a zadejte data pro nový kurzu:</span><span class="sxs-lookup"><span data-stu-id="33c0a-142">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Stránka pro vytvoření kurzu](update-related-data/_static/course-create.png)

<span data-ttu-id="33c0a-144">Klikněte na možnost **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="33c0a-144">Click **Create**.</span></span> <span data-ttu-id="33c0a-145">Kurzy indexovou stránku se zobrazí nové kurzu přidat do seznamu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-145">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="33c0a-146">Název oddělení v seznamu Index stránky pochází z navigační vlastnosti zobrazující, že byla správně vytvoří vztah.</span><span class="sxs-lookup"><span data-stu-id="33c0a-146">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="33c0a-147">Klikněte na tlačítko **upravit** na kurz v kurzy indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="33c0a-147">Click **Edit** on a course in the Courses Index page.</span></span>

![Kurz stránky pro úpravu](update-related-data/_static/course-edit.png)

<span data-ttu-id="33c0a-149">Data na stránce a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="33c0a-149">Change data on the page and click **Save**.</span></span> <span data-ttu-id="33c0a-150">S daty aktualizovaný kurz se zobrazí stránka kurzy indexu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-150">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-instructors-edit-page"></a><span data-ttu-id="33c0a-151">Přidat Instruktoři upravit stránku</span><span class="sxs-lookup"><span data-stu-id="33c0a-151">Add Instructors Edit page</span></span>

<span data-ttu-id="33c0a-152">Při úpravě záznamu instruktorem, budete chtít být schopen aktualizovat přiřazení kanceláře instruktorem.</span><span class="sxs-lookup"><span data-stu-id="33c0a-152">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="33c0a-153">Entita kurzů vedených má jeden nula nebo m relaci s entitou OfficeAssignment, což znamená, že má váš kód pro zpracování těchto situacích:</span><span class="sxs-lookup"><span data-stu-id="33c0a-153">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="33c0a-154">Pokud uživatel vymaže přiřazení kanceláře a původně hodnotu, odstraňte OfficeAssignment entity.</span><span class="sxs-lookup"><span data-stu-id="33c0a-154">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="33c0a-155">Pokud uživatel zadá hodnotu přiřazení office a ho původně byla prázdná, vytvořte novou entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="33c0a-155">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="33c0a-156">Pokud uživatel změní hodnotu přiřazení sady office, změňte hodnotu v existující OfficeAssignment entity.</span><span class="sxs-lookup"><span data-stu-id="33c0a-156">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="33c0a-157">Aktualizace kontroleru Instruktoři</span><span class="sxs-lookup"><span data-stu-id="33c0a-157">Update the Instructors controller</span></span>

<span data-ttu-id="33c0a-158">V *InstructorsController.cs*, změňte kód třídy MetadataExchangeClientMode `Edit` metodu tak, že načte entity kurzů vedených `OfficeAssignment` navigační vlastnost a volání `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="33c0a-158">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

<span data-ttu-id="33c0a-159">Nahraďte HttpPost `Edit` metody následující kód pro zpracování aktualizací přiřazení sady office:</span><span class="sxs-lookup"><span data-stu-id="33c0a-159">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="33c0a-160">Kód provede následující akce:</span><span class="sxs-lookup"><span data-stu-id="33c0a-160">The code does the following:</span></span>

* <span data-ttu-id="33c0a-161">Změní název metody k `EditPost` protože podpis je teď stejně jako třídy MetadataExchangeClientMode `Edit` – metoda ( `ActionName` atribut určuje, že `/Edit/` adresa URL se stále používá).</span><span class="sxs-lookup"><span data-stu-id="33c0a-161">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

* <span data-ttu-id="33c0a-162">Získá aktuální kurzů vedených entit z databáze pomocí nemůžou dočkat, až načítání pro `OfficeAssignment` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="33c0a-162">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="33c0a-163">To je stejný jako u třídy MetadataExchangeClientMode `Edit` metody.</span><span class="sxs-lookup"><span data-stu-id="33c0a-163">This is the same as what you did in the HttpGet `Edit` method.</span></span>

* <span data-ttu-id="33c0a-164">Načtená entita kurzů vedených aktualizuje s hodnotami z vazače modelu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-164">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="33c0a-165">`TryUpdateModel` Přetížení vám umožní přidat na seznam povolených vlastnosti, které chcete zahrnout.</span><span class="sxs-lookup"><span data-stu-id="33c0a-165">The `TryUpdateModel` overload enables you to whitelist the properties you want to include.</span></span> <span data-ttu-id="33c0a-166">To zabraňuje typu over-pass-the účtování, jak je vysvětleno v [druhé části kurzu](crud.md).</span><span class="sxs-lookup"><span data-stu-id="33c0a-166">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* <span data-ttu-id="33c0a-167">Pokud office umístění je prázdné, nastaví vlastnost Instructor.OfficeAssignment na hodnotu null, tak, aby související řádek v tabulce OfficeAssignment se odstraní.</span><span class="sxs-lookup"><span data-stu-id="33c0a-167">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* <span data-ttu-id="33c0a-168">Uloží změny do databáze.</span><span class="sxs-lookup"><span data-stu-id="33c0a-168">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="33c0a-169">Aktualizace kurzů vedených upravit zobrazení</span><span class="sxs-lookup"><span data-stu-id="33c0a-169">Update the Instructor Edit view</span></span>

<span data-ttu-id="33c0a-170">V *Views/Instructors/Edit.cshtml*, přidat nové pole pro úpravy pobočce, na konci před **Uložit** tlačítka:</span><span class="sxs-lookup"><span data-stu-id="33c0a-170">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="33c0a-171">Spusťte aplikaci, vyberte **Instruktoři** kartu a potom klikněte na tlačítko **upravit** na instruktorem.</span><span class="sxs-lookup"><span data-stu-id="33c0a-171">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="33c0a-172">Změnit **pobočce** a klikněte na tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="33c0a-172">Change the **Office Location** and click **Save**.</span></span>

![Stránky pro úpravu instruktorem](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a><span data-ttu-id="33c0a-174">Přidejte do stránky pro úpravu kurzy</span><span class="sxs-lookup"><span data-stu-id="33c0a-174">Add courses to Edit page</span></span>

<span data-ttu-id="33c0a-175">Instruktoři může představuje libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="33c0a-175">Instructors may teach any number of courses.</span></span> <span data-ttu-id="33c0a-176">Teď budete vylepšit kurzů vedených upravit stránku tak, že přidáte změnit přiřazení kurz pomocí skupiny zaškrtávacích políček, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="33c0a-176">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Stránky pro úpravu instruktorem s kurzy](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="33c0a-178">Vztah mezi entitami kurzu a kurzů vedených je many-to-many.</span><span class="sxs-lookup"><span data-stu-id="33c0a-178">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="33c0a-179">Pokud chcete přidat a odebrat relace, přidávat a odebírat entity do a ze sady entit CourseAssignments spojení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-179">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="33c0a-180">Rozhraní, které vám umožní měnit které kurzy instruktorem je přiřazená je skupina zaškrtávacích políček.</span><span class="sxs-lookup"><span data-stu-id="33c0a-180">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="33c0a-181">Zaškrtávací políčko pro každý kurz v databázi se zobrazí a jsou ty, které se kurzů vedených je aktuálně přiřazená k vybrané.</span><span class="sxs-lookup"><span data-stu-id="33c0a-181">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="33c0a-182">Uživatel může vyberte nebo zrušte zaškrtnutí políček, chcete-li změnit přiřazení kurzu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-182">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="33c0a-183">Kdyby mnohem větší počet kurzů, by pravděpodobně chtít použít jinou metodu prezentace dat v zobrazení, ale stejnou metodu manipulace s spojení entity byste použili k vytvoření nebo odstranění vztahů.</span><span class="sxs-lookup"><span data-stu-id="33c0a-183">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="33c0a-184">Aktualizace kontroleru Instruktoři</span><span class="sxs-lookup"><span data-stu-id="33c0a-184">Update the Instructors controller</span></span>

<span data-ttu-id="33c0a-185">Pro zadání dat pro zobrazení seznamu zaškrtávacích políček, použijete třídu modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-185">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="33c0a-186">Vytvoření *AssignedCourseData.cs* v *SchoolViewModels* složky a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="33c0a-186">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="33c0a-187">V *InstructorsController.cs*, nahraďte třídy MetadataExchangeClientMode `Edit` metodu s následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="33c0a-187">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="33c0a-188">Změny jsou zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="33c0a-188">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="33c0a-189">Kód přidá předběžné načítání pro `Courses` navigační vlastnost a volá novou `PopulateAssignedCourseData` metodu k dispozici informaci pro použití pole zaškrtávací políčko `AssignedCourseData` zobrazení třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-189">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="33c0a-190">Kód v `PopulateAssignedCourseData` metoda přečte všechny entity kurzu-li načíst seznam kurzů horizontálních oddílů pomocí třídy modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-190">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="33c0a-191">Jednotlivých kurzů, kód kontroluje, zda existuje kurz v kurzů vedených `Courses` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="33c0a-191">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="33c0a-192">Chcete-li vytvořit efektivní vyhledávání při kontrole, zda je přiřazen kurzů vedených kurz, kurzy přiřazená kurzů vedených jsou vloženy do `HashSet` kolekce.</span><span class="sxs-lookup"><span data-stu-id="33c0a-192">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="33c0a-193">`Assigned` Je nastavena na hodnotu true pro kurzy kurzů vedených přiřazen.</span><span class="sxs-lookup"><span data-stu-id="33c0a-193">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="33c0a-194">Zobrazení bude tuto vlastnost použít k určení, které Kontrola polí musí být zobrazen jako vybraný.</span><span class="sxs-lookup"><span data-stu-id="33c0a-194">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="33c0a-195">Nakonec je předán zobrazení v seznamu `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="33c0a-195">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="33c0a-196">Dál přidejte kód, který se spouští, když uživatel klikne **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="33c0a-196">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="33c0a-197">Nahraďte `EditPost` metoda s tímto kódem a přidejte novou metodu, která aktualizuje `Courses` navigační vlastnost entity instruktorem.</span><span class="sxs-lookup"><span data-stu-id="33c0a-197">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="33c0a-198">Podpis metody je nyní liší od třídy MetadataExchangeClientMode `Edit` metodu, tak název metody se změní z `EditPost` zpět `Edit`.</span><span class="sxs-lookup"><span data-stu-id="33c0a-198">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="33c0a-199">Protože zobrazení neobsahuje kolekci entit kurz, vazače modelu se nedají automaticky aktualizovat `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="33c0a-199">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="33c0a-200">Namísto použití vazače modelu k aktualizaci `CourseAssignments` navigační vlastnost, která učiníte v novém `UpdateInstructorCourses` metoda.</span><span class="sxs-lookup"><span data-stu-id="33c0a-200">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="33c0a-201">Proto je třeba vyloučit `CourseAssignments` vlastnost z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-201">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="33c0a-202">To nevyžaduje změny kódu, který volá `TryUpdateModel` vzhledem k tomu, že používáte přetížení přidávání na seznam povolených a `CourseAssignments` není v seznamu pro zahrnutí.</span><span class="sxs-lookup"><span data-stu-id="33c0a-202">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="33c0a-203">Pokud není žádná kontrola se zaškrtnuta, kód v `UpdateInstructorCourses` inicializuje `CourseAssignments` navigační vlastnost s prázdnou kolekci a vrátí:</span><span class="sxs-lookup"><span data-stu-id="33c0a-203">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="33c0a-204">Kód pak projde všechny kurzy v databázi a zkontroluje každý kurz oproti těm, které jsou přiřazeny k instruktorem a ty, které byly vybrány v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="33c0a-204">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="33c0a-205">K usnadnění efektivnější vyhledávání, druhé dvě kolekce jsou uloženy v `HashSet` objekty.</span><span class="sxs-lookup"><span data-stu-id="33c0a-205">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="33c0a-206">Pokud zaškrtli políčko pro kurz ale kurzu není v `Instructor.CourseAssignments` navigační vlastnost, kurz je přidán do kolekce ve vlastnosti navigace.</span><span class="sxs-lookup"><span data-stu-id="33c0a-206">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="33c0a-207">Pokud není zaškrtnuté políčko kurzům, ale celé probíhá `Instructor.CourseAssignments` navigační vlastnost, kurz je odebrána z navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="33c0a-207">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="33c0a-208">Aktualizace zobrazení instruktorem</span><span class="sxs-lookup"><span data-stu-id="33c0a-208">Update the Instructor views</span></span>

<span data-ttu-id="33c0a-209">V *Views/Instructors/Edit.cshtml*, přidat **kurzy** pole s polem zaškrtávacích políček přidáním následujícího kódu ihned po `div` prvky pro **Office**  pole a před `div` – element pro **Uložit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="33c0a-209">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="33c0a-210">Při vkládání kódu v sadě Visual Studio konce řádků se změní tak, aby kód přestane fungovat.</span><span class="sxs-lookup"><span data-stu-id="33c0a-210">When you paste the code in Visual Studio, line breaks will be changed in a way that breaks the code.</span></span> <span data-ttu-id="33c0a-211">Stisknutím klávesy Ctrl + Z jednou vrátit zpět, automatického formátování.</span><span class="sxs-lookup"><span data-stu-id="33c0a-211">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="33c0a-212">To tak, aby vypadají se zobrazí zde opraví konce řádků.</span><span class="sxs-lookup"><span data-stu-id="33c0a-212">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="33c0a-213">Odsazení nemusí být dokonalý, ale `@</tr><tr>`, `@:<td>`, `@:</td>`, a `@:</tr>` řádky musí být na jednom řádku znázorněno nebo zobrazí se chyba za běhu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-213">The indentation doesn't have to be perfect, but the `@</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="33c0a-214">Blok vybrali nový kód a stisknutím klávesy Tab třikrát na řádek nahoru nový kód existující kód.</span><span class="sxs-lookup"><span data-stu-id="33c0a-214">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="33c0a-215">Stav tohoto problému zjistíte [tady](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="33c0a-215">You can check the status of this problem [here](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="33c0a-216">Tento kód vytvoří tabulku HTML, který obsahuje tři sloupce.</span><span class="sxs-lookup"><span data-stu-id="33c0a-216">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="33c0a-217">V každém sloupci, představuje zaškrtávací políčko, za nímž následuje, který se skládá z kurzu počet a názvy popisků.</span><span class="sxs-lookup"><span data-stu-id="33c0a-217">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="33c0a-218">Všechna zaškrtávací políčka mají stejný název ("selectedCourses"), která informuje o vazač modelu jsou považovány za skupinu.</span><span class="sxs-lookup"><span data-stu-id="33c0a-218">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="33c0a-219">Hodnota atributu každé zaškrtávací políčko je nastavena na hodnotu `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="33c0a-219">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="33c0a-220">Na stránce, když se pošle vazače modelu předá kontroler, který se skládá z pole `CourseID` pouze zaškrtávací políčka, které jsou vybrané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="33c0a-220">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="33c0a-221">Když zaškrtávací políčka jsou zpočátku vykresleno, ty, které jsou pro kurzy, které jsou přiřazeny kurzů vedených kontrole atributů, který vybere je (zobrazí se, které je zaškrtnuté políčko).</span><span class="sxs-lookup"><span data-stu-id="33c0a-221">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="33c0a-222">Spusťte aplikaci, vyberte **Instruktoři** kartu a klikněte na tlačítko **upravit** na instruktorem zobrazíte **upravit** stránky.</span><span class="sxs-lookup"><span data-stu-id="33c0a-222">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Stránky pro úpravu instruktorem s kurzy](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="33c0a-224">Změňte některá přiřazení kurzu a klikněte na Uložit.</span><span class="sxs-lookup"><span data-stu-id="33c0a-224">Change some course assignments and click Save.</span></span> <span data-ttu-id="33c0a-225">Provedené změny se projeví na indexovou stránku.</span><span class="sxs-lookup"><span data-stu-id="33c0a-225">The changes you make are reflected on the Index page.</span></span>

> [!NOTE]
> <span data-ttu-id="33c0a-226">Přístup provádět upravovat data kurzu kurzů vedených funguje dobře, když je omezený počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="33c0a-226">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="33c0a-227">Pro kolekce, které jsou mnohem větší různé uživatelské rozhraní a jinou metodu aktualizace by vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="33c0a-227">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-delete-page"></a><span data-ttu-id="33c0a-228">Stránka pro aktualizaci Delete</span><span class="sxs-lookup"><span data-stu-id="33c0a-228">Update Delete page</span></span>

<span data-ttu-id="33c0a-229">V *InstructorsController.cs*, odstranit `DeleteConfirmed` metoda a vložte následující kód na příslušné místo.</span><span class="sxs-lookup"><span data-stu-id="33c0a-229">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="33c0a-230">Tento kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="33c0a-230">This code makes the following changes:</span></span>

* <span data-ttu-id="33c0a-231">Fakturuje se u eager načítání pro `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="33c0a-231">Does eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="33c0a-232">Je nutné zahrnout to nebo EF nebude vědět o související `CourseAssignment` entity a nedojde k jejich odstranění.</span><span class="sxs-lookup"><span data-stu-id="33c0a-232">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span> <span data-ttu-id="33c0a-233">Abyste nemuseli přečtěte si je tady můžete nakonfigurovat kaskádové odstranění v databázi.</span><span class="sxs-lookup"><span data-stu-id="33c0a-233">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="33c0a-234">Pokud instruktorem, která se má odstranit je přiřazen jako správce z jakékoli oddělení, odebere z těchto oddělení přiřazení instruktorem.</span><span class="sxs-lookup"><span data-stu-id="33c0a-234">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-create-page"></a><span data-ttu-id="33c0a-235">Přidat stránku vytvořit pobočky a kurzy</span><span class="sxs-lookup"><span data-stu-id="33c0a-235">Add office location and courses to Create page</span></span>

<span data-ttu-id="33c0a-236">V *InstructorsController.cs*, odstraňte HttpGet a HttpPost `Create` metody a místo nich přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="33c0a-236">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="33c0a-237">Tento kód se podobá viděli pro `Edit` jsou vybrané metody s výjimkou případů, které žádné kurzy.</span><span class="sxs-lookup"><span data-stu-id="33c0a-237">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="33c0a-238">Třídy MetadataExchangeClientMode `Create` volání metod `PopulateAssignedCourseData` metoda není, protože může být ale vybrané kurzy účelem zadejte prázdnou kolekci pro `foreach` smyčky v zobrazení (jinak zobrazit kód by výjimku výjimky odkaz s hodnotou null).</span><span class="sxs-lookup"><span data-stu-id="33c0a-238">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="33c0a-239">HttpPost `Create` metoda přidá každé vybrané kurz `CourseAssignments` navigační vlastnost před kontroluje výskyt chyb ověření a přidá nový kurzů vedených do databáze.</span><span class="sxs-lookup"><span data-stu-id="33c0a-239">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="33c0a-240">Kurzy jsou přidány, i když nejsou chyby modelu, takže když existují chyby modelu (pro příklad, uživatel s klíčem, což je neplatné datum) a na stránce se zobrazí znovu, zobrazí se chybová zpráva, se automaticky obnoví zvolené kurzů, které byly provedeny.</span><span class="sxs-lookup"><span data-stu-id="33c0a-240">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="33c0a-241">Všimněte si, aby bylo možné přidat kurzy, které `CourseAssignments` navigační vlastnost, je třeba inicializovat vlastnost jako prázdnou kolekci:</span><span class="sxs-lookup"><span data-stu-id="33c0a-241">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="33c0a-242">Jako alternativu k to v kódu kontroleru je může provést v modelu kurzů vedených změnou metoda getter vlastnosti pro vytvoření kolekce Pokud neexistuje, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="33c0a-242">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

<span data-ttu-id="33c0a-243">Pokud změníte `CourseAssignments` vlastnost tímto způsobem můžete odebrat explicitní vlastnost inicializační kód v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="33c0a-243">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="33c0a-244">V *Views/Instructor/Create.cshtml*, přidejte textové pole umístění office a zaškrtněte políčka pro kurzy před tlačítka pro odeslání.</span><span class="sxs-lookup"><span data-stu-id="33c0a-244">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="33c0a-245">Třeba v případě stránky pro úpravu [opravte formátování Pokud Visual Studio přeformátuje kód při vkládání](#notepad).</span><span class="sxs-lookup"><span data-stu-id="33c0a-245">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="33c0a-246">Testování tak, že aplikaci spustíte a vytváření instruktorem.</span><span class="sxs-lookup"><span data-stu-id="33c0a-246">Test by running the app and creating an instructor.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="33c0a-247">Zpracování transakcí</span><span class="sxs-lookup"><span data-stu-id="33c0a-247">Handling Transactions</span></span>

<span data-ttu-id="33c0a-248">Jak je vysvětleno v [CRUD kurzu](crud.md), Entity Framework implementuje implicitně transakce.</span><span class="sxs-lookup"><span data-stu-id="33c0a-248">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="33c0a-249">Pro scénáře, kde můžete potřebovat mít lepší kontrolu – například pokud budete chtít zahrnout operace provedené mimo rozhraní Entity Framework v rámci transakce – viz [transakce](/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="33c0a-249">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="33c0a-250">Získat kód</span><span class="sxs-lookup"><span data-stu-id="33c0a-250">Get the code</span></span>

[<span data-ttu-id="33c0a-251">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="33c0a-251">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="33c0a-252">Další kroky</span><span class="sxs-lookup"><span data-stu-id="33c0a-252">Next steps</span></span>

<span data-ttu-id="33c0a-253">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="33c0a-253">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="33c0a-254">Vlastní stránky kurzy</span><span class="sxs-lookup"><span data-stu-id="33c0a-254">Customized Courses pages</span></span>
> * <span data-ttu-id="33c0a-255">Přidání Instruktoři upravit stránku</span><span class="sxs-lookup"><span data-stu-id="33c0a-255">Added Instructors Edit page</span></span>
> * <span data-ttu-id="33c0a-256">Přidání kurzy, které stránky pro úpravu</span><span class="sxs-lookup"><span data-stu-id="33c0a-256">Added courses to Edit page</span></span>
> * <span data-ttu-id="33c0a-257">Aktualizovaná stránka Delete</span><span class="sxs-lookup"><span data-stu-id="33c0a-257">Updated Delete page</span></span>
> * <span data-ttu-id="33c0a-258">Přidání pobočky a kurzy, které stránka pro vytvoření</span><span class="sxs-lookup"><span data-stu-id="33c0a-258">Added office location and courses to Create page</span></span>

<span data-ttu-id="33c0a-259">Přejděte k dalším článku se naučíte, jak zpracování konfliktů souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="33c0a-259">Advance to the next article to learn how to handle concurrency conflicts.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="33c0a-260">Zpracování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="33c0a-260">Handle concurrency conflicts</span></span>](concurrency.md)
