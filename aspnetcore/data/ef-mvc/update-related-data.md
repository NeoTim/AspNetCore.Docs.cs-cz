---
title: 'Kurz: Aktualizace souvisejících dat – ASP.NET MVC pomocí EF Core'
description: V tomto kurzu aktualizujete související data aktualizací polí cizího klíče a vlastností navigace.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: 215cf04e882c438eac3fb74ee7cd582e96ddaeb7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018023"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="8f51d-103">Kurz: Aktualizace souvisejících dat – ASP.NET MVC pomocí EF Core</span><span class="sxs-lookup"><span data-stu-id="8f51d-103">Tutorial: Update related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="8f51d-104">V předchozím kurzu jste zobrazili související data. v tomto kurzu aktualizujete související data aktualizací polí cizího klíče a vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="8f51d-104">In the previous tutorial you displayed related data; in this tutorial you'll update related data by updating foreign key fields and navigation properties.</span></span>

<span data-ttu-id="8f51d-105">Následující ilustrace znázorňují některé stránky, se kterými budete pracovat.</span><span class="sxs-lookup"><span data-stu-id="8f51d-105">The following illustrations show some of the pages that you'll work with.</span></span>

![Stránka pro úpravu kurzu](update-related-data/_static/course-edit.png)

![Stránka pro úpravu instruktora](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="8f51d-108">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="8f51d-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8f51d-109">Přizpůsobení stránek kurzů</span><span class="sxs-lookup"><span data-stu-id="8f51d-109">Customize Courses pages</span></span>
> * <span data-ttu-id="8f51d-110">Přidat stránku pro úpravu instruktorů</span><span class="sxs-lookup"><span data-stu-id="8f51d-110">Add Instructors Edit page</span></span>
> * <span data-ttu-id="8f51d-111">Přidání kurzů pro úpravu stránky</span><span class="sxs-lookup"><span data-stu-id="8f51d-111">Add courses to Edit page</span></span>
> * <span data-ttu-id="8f51d-112">Aktualizace stránky pro odstranění</span><span class="sxs-lookup"><span data-stu-id="8f51d-112">Update Delete page</span></span>
> * <span data-ttu-id="8f51d-113">Přidání umístění Office a kurzů k vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="8f51d-113">Add office location and courses to Create page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8f51d-114">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8f51d-114">Prerequisites</span></span>

* [<span data-ttu-id="8f51d-115">Čtení souvisejících dat</span><span class="sxs-lookup"><span data-stu-id="8f51d-115">Read related data</span></span>](read-related-data.md)

## <a name="customize-courses-pages"></a><span data-ttu-id="8f51d-116">Přizpůsobení stránek kurzů</span><span class="sxs-lookup"><span data-stu-id="8f51d-116">Customize Courses pages</span></span>

<span data-ttu-id="8f51d-117">Když se vytvoří nová entita kurzu, musí mít relaci s existujícím oddělením.</span><span class="sxs-lookup"><span data-stu-id="8f51d-117">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="8f51d-118">Pro usnadnění tohoto kódu se vygenerovaný kód skládá z metod kontroleru a vytváření a upravování zobrazení, která obsahují rozevírací seznam pro výběr oddělení.</span><span class="sxs-lookup"><span data-stu-id="8f51d-118">To facilitate this, the scaffolded code includes controller methods and Create and Edit views that include a drop-down list for selecting the department.</span></span> <span data-ttu-id="8f51d-119">Rozevírací seznam nastaví `Course.DepartmentID` vlastnost cizího klíče a to je vše, co Entity Framework potřebuje, aby se vlastnost navigace načetla `Department` s příslušnou entitou oddělení.</span><span class="sxs-lookup"><span data-stu-id="8f51d-119">The drop-down list sets the `Course.DepartmentID` foreign key property, and that's all the Entity Framework needs in order to load the `Department` navigation property with the appropriate Department entity.</span></span> <span data-ttu-id="8f51d-120">Použijete generovaný kód, ale mírně ho změníte a přidáte tak zpracování chyb a seřadíte rozevírací seznam.</span><span class="sxs-lookup"><span data-stu-id="8f51d-120">You'll use the scaffolded code, but change it slightly to add error handling and sort the drop-down list.</span></span>

<span data-ttu-id="8f51d-121">V *CoursesController.cs*odstraňte čtyři metody Create a Edit a nahraďte je následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8f51d-121">In *CoursesController.cs*, delete the four Create and Edit methods and replace them with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

<span data-ttu-id="8f51d-122">Po `Edit` metodě HTTPPOST vytvořte novou metodu, která načte informace o oddělení v rozevíracím seznamu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-122">After the `Edit` HttpPost method, create a new method that loads department info for the drop-down list.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

<span data-ttu-id="8f51d-123">`PopulateDepartmentsDropDownList`Metoda získá seznam všech oddělení seřazených podle názvu, vytvoří `SelectList` kolekci pro rozevírací seznam a předá kolekci do zobrazení v `ViewBag` .</span><span class="sxs-lookup"><span data-stu-id="8f51d-123">The `PopulateDepartmentsDropDownList` method gets a list of all departments sorted by name, creates a `SelectList` collection for a drop-down list, and passes the collection to the view in `ViewBag`.</span></span> <span data-ttu-id="8f51d-124">Metoda přijímá volitelný `selectedDepartment` parametr, který umožňuje volajícímu kódu určit položku, která bude vybrána při vykreslení rozevíracího seznamu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-124">The method accepts the optional `selectedDepartment` parameter that allows the calling code to specify the item that will be selected when the drop-down list is rendered.</span></span> <span data-ttu-id="8f51d-125">Zobrazení pojmenuje název "DepartmentID" do `<select>` pomocné rutiny značky a pomoc pak ví, aby hledal `ViewBag` objekt pro `SelectList` pojmenování "DepartmentID".</span><span class="sxs-lookup"><span data-stu-id="8f51d-125">The view will pass the name "DepartmentID" to the `<select>` tag helper, and the helper then knows to look in the `ViewBag` object for a `SelectList` named "DepartmentID".</span></span>

<span data-ttu-id="8f51d-126">Metoda HttpGet `Create` volá `PopulateDepartmentsDropDownList` metodu bez nastavení vybrané položky, protože pro nový kurz se oddělení ještě nevytvořilo:</span><span class="sxs-lookup"><span data-stu-id="8f51d-126">The HttpGet `Create` method calls the `PopulateDepartmentsDropDownList` method without setting the selected item, because for a new course the department isn't established yet:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

<span data-ttu-id="8f51d-127">Metoda HttpGet `Edit` Nastaví vybranou položku na základě ID oddělení, které je už přiřazené k upravovanému kurzu:</span><span class="sxs-lookup"><span data-stu-id="8f51d-127">The HttpGet `Edit` method sets the selected item, based on the ID of the department that's already assigned to the course being edited:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

<span data-ttu-id="8f51d-128">Metody HttpPost pro obojí `Create` a `Edit` také obsahují kód, který nastaví vybranou položku, když znovu zobrazí stránku po chybě.</span><span class="sxs-lookup"><span data-stu-id="8f51d-128">The HttpPost methods for both `Create` and `Edit` also include code that sets the selected item when they redisplay the page after an error.</span></span> <span data-ttu-id="8f51d-129">Tím se zajistí, že se při zobrazení stránky zobrazí chybová zpráva bez ohledu na vybrané oddělení zůstane vybraná možnost.</span><span class="sxs-lookup"><span data-stu-id="8f51d-129">This ensures that when the page is redisplayed to show the error message, whatever department was selected stays selected.</span></span>

### <a name="add-asnotracking-to-details-and-delete-methods"></a><span data-ttu-id="8f51d-130">Přidávání. AsNoTracking metody Details a DELETE</span><span class="sxs-lookup"><span data-stu-id="8f51d-130">Add .AsNoTracking to Details and Delete methods</span></span>

<span data-ttu-id="8f51d-131">Pokud chcete optimalizovat výkon pro podrobnosti kurzu a odstranit stránky, přidejte `AsNoTracking` volání `Details` metod a HttpGet `Delete` .</span><span class="sxs-lookup"><span data-stu-id="8f51d-131">To optimize performance of the Course Details and Delete pages, add `AsNoTracking` calls in the `Details` and HttpGet `Delete` methods.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a><span data-ttu-id="8f51d-132">Úprava zobrazení kurzu</span><span class="sxs-lookup"><span data-stu-id="8f51d-132">Modify the Course views</span></span>

<span data-ttu-id="8f51d-133">V okně *zobrazení/kurzy/vytvořit. cshtml*přidejte možnost "vybrat oddělení" do rozevíracího seznamu **oddělení** , změňte titulek z **DepartmentID** na **oddělení**a přidejte ověřovací zprávu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-133">In *Views/Courses/Create.cshtml*, add a "Select Department" option to the **Department** drop-down list, change the caption from **DepartmentID** to **Department**, and add a validation message.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

<span data-ttu-id="8f51d-134">V *zobrazeních, kurzech/úpravách. cshtml*udělejte stejnou změnu pro pole oddělení, které jste právě vytvořili v části *vytvoření. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8f51d-134">In *Views/Courses/Edit.cshtml*, make the same change for the Department field that you just did in *Create.cshtml*.</span></span>

<span data-ttu-id="8f51d-135">Také v *zobrazeních/kurzech/upravit. cshtml*přidejte pole číslo kurzu před pole **název** .</span><span class="sxs-lookup"><span data-stu-id="8f51d-135">Also in *Views/Courses/Edit.cshtml*, add a course number field before the **Title** field.</span></span> <span data-ttu-id="8f51d-136">Vzhledem k tomu, že číslo kurzu je primární klíč, zobrazuje se, ale nedá se změnit.</span><span class="sxs-lookup"><span data-stu-id="8f51d-136">Because the course number is the primary key, it's displayed, but it can't be changed.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

<span data-ttu-id="8f51d-137">V zobrazení pro úpravy již existuje skryté pole ( `<input type="hidden">` ) pro číslo kurzu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-137">There's already a hidden field (`<input type="hidden">`) for the course number in the Edit view.</span></span> <span data-ttu-id="8f51d-138">Přidáním `<label>` pomocníka značek se eliminuje nutnost skrytého pole, protože nezpůsobí, že se číslo kurzu zahrne do publikovaných dat, když uživatel klikne na **Uložit** na stránce **Upravit** .</span><span class="sxs-lookup"><span data-stu-id="8f51d-138">Adding a `<label>` tag helper doesn't eliminate the need for the hidden field because it doesn't cause the course number to be included in the posted data when the user clicks **Save** on the **Edit** page.</span></span>

<span data-ttu-id="8f51d-139">V *zobrazení/kurzy/odstranit. cshtml*přidejte do horní části pole číslo kurzu a změňte ID oddělení na název oddělení.</span><span class="sxs-lookup"><span data-stu-id="8f51d-139">In *Views/Courses/Delete.cshtml*, add a course number field at the top and change department ID to department name.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

<span data-ttu-id="8f51d-140">V *zobrazeních/kurzech/details. cshtml*udělejte stejnou změnu, kterou jste právě provedli pro *odstranění. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8f51d-140">In *Views/Courses/Details.cshtml*, make the same change that you just did for *Delete.cshtml*.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="8f51d-141">Testování stránek kurzu</span><span class="sxs-lookup"><span data-stu-id="8f51d-141">Test the Course pages</span></span>

<span data-ttu-id="8f51d-142">Spusťte aplikaci, vyberte kartu **kurzy** , klikněte na **vytvořit novou**a zadejte data nového kurzu:</span><span class="sxs-lookup"><span data-stu-id="8f51d-142">Run the app, select the **Courses** tab, click **Create New**, and enter data for a new course:</span></span>

![Stránka vytvoření kurzu](update-related-data/_static/course-create.png)

<span data-ttu-id="8f51d-144">Klikněte na **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="8f51d-144">Click **Create**.</span></span> <span data-ttu-id="8f51d-145">Stránka s rejstříkem kurzů se zobrazí spolu s novým kurzem přidaným do seznamu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-145">The Courses Index page is displayed with the new course added to the list.</span></span> <span data-ttu-id="8f51d-146">Název oddělení v seznamu stránek indexu pochází z navigační vlastnosti, která ukazuje, že relace byla správně vytvořena.</span><span class="sxs-lookup"><span data-stu-id="8f51d-146">The department name in the Index page list comes from the navigation property, showing that the relationship was established correctly.</span></span>

<span data-ttu-id="8f51d-147">V kurzu na stránce s rejstříkem kurzů klikněte na **Upravit** .</span><span class="sxs-lookup"><span data-stu-id="8f51d-147">Click **Edit** on a course in the Courses Index page.</span></span>

![Stránka pro úpravu kurzu](update-related-data/_static/course-edit.png)

<span data-ttu-id="8f51d-149">Změňte data na stránce a klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="8f51d-149">Change data on the page and click **Save**.</span></span> <span data-ttu-id="8f51d-150">Stránka s rejstříkem kurzů se zobrazuje s aktualizovanými daty kurzu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-150">The Courses Index page is displayed with the updated course data.</span></span>

## <a name="add-instructors-edit-page"></a><span data-ttu-id="8f51d-151">Přidat stránku pro úpravu instruktorů</span><span class="sxs-lookup"><span data-stu-id="8f51d-151">Add Instructors Edit page</span></span>

<span data-ttu-id="8f51d-152">Když upravíte záznam instruktora, chcete mít schopnost aktualizovat přiřazení kanceláře instruktora.</span><span class="sxs-lookup"><span data-stu-id="8f51d-152">When you edit an instructor record, you want to be able to update the instructor's office assignment.</span></span> <span data-ttu-id="8f51d-153">Entita Instructor má relaci 1:1 s entitou OfficeAssignment, což znamená, že váš kód musí zpracovávat následující situace:</span><span class="sxs-lookup"><span data-stu-id="8f51d-153">The Instructor entity has a one-to-zero-or-one relationship with the OfficeAssignment entity, which means your code has to handle the following situations:</span></span>

* <span data-ttu-id="8f51d-154">Pokud uživatel zruší přiřazení Office a původně měl hodnotu, odstraňte entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="8f51d-154">If the user clears the office assignment and it originally had a value, delete the OfficeAssignment entity.</span></span>

* <span data-ttu-id="8f51d-155">Pokud uživatel zadá hodnotu přiřazení Office, která byla původně prázdná, vytvořte novou entitu OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="8f51d-155">If the user enters an office assignment value and it originally was empty, create a new OfficeAssignment entity.</span></span>

* <span data-ttu-id="8f51d-156">Pokud uživatel změní hodnotu přiřazení kanceláře, změňte hodnotu v existující entitě OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="8f51d-156">If the user changes the value of an office assignment, change the value in an existing OfficeAssignment entity.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="8f51d-157">Aktualizace kontroleru instruktorů</span><span class="sxs-lookup"><span data-stu-id="8f51d-157">Update the Instructors controller</span></span>

<span data-ttu-id="8f51d-158">V *InstructorsController.cs*změňte kód v `Edit` metodě HttpGet tak, aby byla načtena `OfficeAssignment` vlastnost navigace a volání entity instruktora `AsNoTracking` :</span><span class="sxs-lookup"><span data-stu-id="8f51d-158">In *InstructorsController.cs*, change the code in the HttpGet `Edit` method so that it loads the Instructor entity's `OfficeAssignment` navigation property and calls `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=8-11&name=snippet_EditGetOA)]

<span data-ttu-id="8f51d-159">`Edit`Chcete-li zpracovat aktualizace přiřazení Office, nahraďte metodu HTTPPOST následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8f51d-159">Replace the HttpPost `Edit` method with the following code to handle office assignment updates:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

<span data-ttu-id="8f51d-160">Kód provede následující:</span><span class="sxs-lookup"><span data-stu-id="8f51d-160">The code does the following:</span></span>

* <span data-ttu-id="8f51d-161">Změní název metody na, `EditPost` protože signatura je nyní stejná jako `Edit` Metoda HttpGet ( `ActionName` atribut určuje, že se `/Edit/` Adresa URL stále používá).</span><span class="sxs-lookup"><span data-stu-id="8f51d-161">Changes the method name to `EditPost` because the signature is now the same as the HttpGet `Edit` method (the `ActionName` attribute specifies that the `/Edit/` URL is still used).</span></span>

* <span data-ttu-id="8f51d-162">Získá aktuální entitu instruktor z databáze pomocí Eager načítání pro `OfficeAssignment` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8f51d-162">Gets the current Instructor entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span> <span data-ttu-id="8f51d-163">To se shoduje s tím, co jste provedli v `Edit` metodě HttpGet.</span><span class="sxs-lookup"><span data-stu-id="8f51d-163">This is the same as what you did in the HttpGet `Edit` method.</span></span>

* <span data-ttu-id="8f51d-164">Aktualizuje načtenou entitu Instructor hodnotami z pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="8f51d-164">Updates the retrieved Instructor entity with values from the model binder.</span></span> <span data-ttu-id="8f51d-165">`TryUpdateModel`Přetížení umožňuje deklarovat vlastnosti, které chcete zahrnout.</span><span class="sxs-lookup"><span data-stu-id="8f51d-165">The `TryUpdateModel` overload enables you to declare the properties you want to include.</span></span> <span data-ttu-id="8f51d-166">Tím zabráníte převzetí služeb při selhání, jak je vysvětleno v [druhém kurzu](crud.md).</span><span class="sxs-lookup"><span data-stu-id="8f51d-166">This prevents over-posting, as explained in the [second tutorial](crud.md).</span></span>

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

* <span data-ttu-id="8f51d-167">Pokud je umístění kanceláře prázdné, vlastnost Instructor. OfficeAssignment nastaví na hodnotu null, aby se související řádek v tabulce OfficeAssignment odstranil.</span><span class="sxs-lookup"><span data-stu-id="8f51d-167">If the office location is blank, sets the Instructor.OfficeAssignment property to null so that the related row in the OfficeAssignment table will be deleted.</span></span>

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

* <span data-ttu-id="8f51d-168">Uloží změny do databáze.</span><span class="sxs-lookup"><span data-stu-id="8f51d-168">Saves the changes to the database.</span></span>

### <a name="update-the-instructor-edit-view"></a><span data-ttu-id="8f51d-169">Aktualizace zobrazení pro úpravy instruktora</span><span class="sxs-lookup"><span data-stu-id="8f51d-169">Update the Instructor Edit view</span></span>

<span data-ttu-id="8f51d-170">V *zobrazeních/instruktorech/upravit. cshtml*přidejte nové pole pro úpravy umístění kanceláře, a to na konci před tlačítkem **Uložit** :</span><span class="sxs-lookup"><span data-stu-id="8f51d-170">In *Views/Instructors/Edit.cshtml*, add a new field for editing the office location, at the end before the **Save** button:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

<span data-ttu-id="8f51d-171">Spusťte aplikaci, vyberte kartu **instruktoři** a potom klikněte na tlačítko **Upravit** v instruktorovi.</span><span class="sxs-lookup"><span data-stu-id="8f51d-171">Run the app, select the **Instructors** tab, and then click **Edit** on an instructor.</span></span> <span data-ttu-id="8f51d-172">Změňte **umístění kanceláře** a klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="8f51d-172">Change the **Office Location** and click **Save**.</span></span>

![Stránka pro úpravu instruktora](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a><span data-ttu-id="8f51d-174">Přidání kurzů pro úpravu stránky</span><span class="sxs-lookup"><span data-stu-id="8f51d-174">Add courses to Edit page</span></span>

<span data-ttu-id="8f51d-175">Instruktoři můžou učit libovolný počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="8f51d-175">Instructors may teach any number of courses.</span></span> <span data-ttu-id="8f51d-176">Nyní vylepšíte stránku pro úpravu instruktoru přidáním možnosti změnit přiřazení kurzů pomocí skupiny zaškrtávacích políček, jak je znázorněno na následujícím snímku obrazovky:</span><span class="sxs-lookup"><span data-stu-id="8f51d-176">Now you'll enhance the Instructor Edit page by adding the ability to change course assignments using a group of check boxes, as shown in the following screen shot:</span></span>

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="8f51d-178">Vztah mezi entitami Course a instruktorem je mnoho až mnoho.</span><span class="sxs-lookup"><span data-stu-id="8f51d-178">The relationship between the Course and Instructor entities is many-to-many.</span></span> <span data-ttu-id="8f51d-179">Chcete-li přidat a odebrat relace, přidejte a odeberte entity do a ze sady entit CourseAssignments JOIN.</span><span class="sxs-lookup"><span data-stu-id="8f51d-179">To add and remove relationships, you add and remove entities to and from the CourseAssignments join entity set.</span></span>

<span data-ttu-id="8f51d-180">Uživatelské rozhraní, které umožňuje změnit, ke kterým kurzům je instruktor přiřazen, je skupina zaškrtávacích políček.</span><span class="sxs-lookup"><span data-stu-id="8f51d-180">The UI that enables you to change which courses an instructor is assigned to is a group of check boxes.</span></span> <span data-ttu-id="8f51d-181">Zobrazí se zaškrtávací políčko pro každý kurz v databázi a jsou vybrány ty, ke kterým je instruktor aktuálně přiřazen.</span><span class="sxs-lookup"><span data-stu-id="8f51d-181">A check box for every course in the database is displayed, and the ones that the instructor is currently assigned to are selected.</span></span> <span data-ttu-id="8f51d-182">Uživatel může zaškrtnutím nebo zrušením zaškrtnutí políček změnit přiřazení kurzů.</span><span class="sxs-lookup"><span data-stu-id="8f51d-182">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="8f51d-183">Pokud byl počet kurzů mnohem větší, pravděpodobně budete chtít použít jinou metodu, která prezentují data v zobrazení, ale použijte stejnou metodu manipulace s entitou JOIN k vytváření nebo odstraňování relací.</span><span class="sxs-lookup"><span data-stu-id="8f51d-183">If the number of courses were much greater, you would probably want to use a different method of presenting the data in the view, but you'd use the same method of manipulating a join entity to create or delete relationships.</span></span>

### <a name="update-the-instructors-controller"></a><span data-ttu-id="8f51d-184">Aktualizace kontroleru instruktorů</span><span class="sxs-lookup"><span data-stu-id="8f51d-184">Update the Instructors controller</span></span>

<span data-ttu-id="8f51d-185">Chcete-li poskytnout data do zobrazení pro seznam zaškrtávacích políček, použijte třídu zobrazení modelu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-185">To provide data to the view for the list of check boxes, you'll use a view model class.</span></span>

<span data-ttu-id="8f51d-186">Vytvořte *AssignedCourseData.cs* ve složce *SchoolViewModels* a nahraďte existující kód následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8f51d-186">Create *AssignedCourseData.cs* in the *SchoolViewModels* folder and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="8f51d-187">V *InstructorsController.cs*nahraďte metodu HttpGet `Edit` následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="8f51d-187">In *InstructorsController.cs*, replace the HttpGet `Edit` method with the following code.</span></span> <span data-ttu-id="8f51d-188">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="8f51d-188">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

<span data-ttu-id="8f51d-189">Kód přidá načtení Eager pro `Courses` navigační vlastnost a zavolá `PopulateAssignedCourseData` metodu New k poskytnutí informací pro pole zaškrtávacího políčka pomocí `AssignedCourseData` třídy zobrazení modelu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-189">The code adds eager loading for the `Courses` navigation property and calls the new `PopulateAssignedCourseData` method to provide information for the check box array using the `AssignedCourseData` view model class.</span></span>

<span data-ttu-id="8f51d-190">Kód v `PopulateAssignedCourseData` metodě čte prostřednictvím všech entit kurzu, aby bylo možné načíst seznam kurzů pomocí třídy zobrazení modelu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-190">The code in the `PopulateAssignedCourseData` method reads through all Course entities in order to load a list of courses using the view model class.</span></span> <span data-ttu-id="8f51d-191">Pro každý kurz kód kontroluje, zda v navigační vlastnosti instruktora existuje kurz `Courses` .</span><span class="sxs-lookup"><span data-stu-id="8f51d-191">For each course, the code checks whether the course exists in the instructor's `Courses` navigation property.</span></span> <span data-ttu-id="8f51d-192">K vytvoření efektivního vyhledávání při kontrole, jestli je kurz přiřazen instruktorovi, jsou kurzy přiřazené k instruktorovi vloženy do `HashSet` kolekce.</span><span class="sxs-lookup"><span data-stu-id="8f51d-192">To create efficient lookup when checking whether a course is assigned to the instructor, the courses assigned to the instructor are put into a `HashSet` collection.</span></span> <span data-ttu-id="8f51d-193">`Assigned`Vlastnost je nastavena na hodnotu true pro kurzy, ke kterým je instruktor přiřazen.</span><span class="sxs-lookup"><span data-stu-id="8f51d-193">The `Assigned` property  is set to true for courses the instructor is assigned to.</span></span> <span data-ttu-id="8f51d-194">Zobrazení použije tuto vlastnost k určení, která zaškrtávací políčka se musí zobrazit jako vybraná.</span><span class="sxs-lookup"><span data-stu-id="8f51d-194">The view will use this property to determine which check boxes must be displayed as selected.</span></span> <span data-ttu-id="8f51d-195">Nakonec se seznam předává do zobrazení v `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="8f51d-195">Finally, the list is passed to the view in `ViewData`.</span></span>

<span data-ttu-id="8f51d-196">Dále přidejte kód, který se spustí, když uživatel klikne na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="8f51d-196">Next, add the code that's executed when the user clicks **Save**.</span></span> <span data-ttu-id="8f51d-197">Nahraďte `EditPost` metodu následujícím kódem a přidejte novou metodu, která aktualizuje `Courses` navigační vlastnost entity Instructor.</span><span class="sxs-lookup"><span data-stu-id="8f51d-197">Replace the `EditPost` method with the following code, and add a new method that updates the `Courses` navigation property of the Instructor entity.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

<span data-ttu-id="8f51d-198">Signatura metody se teď liší od metody HttpGet `Edit` , takže se název metody změní z `EditPost` back na `Edit` .</span><span class="sxs-lookup"><span data-stu-id="8f51d-198">The method signature is now different from the HttpGet `Edit` method, so the method name changes from `EditPost` back to `Edit`.</span></span>

<span data-ttu-id="8f51d-199">Vzhledem k tomu, že zobrazení nemá kolekci entit kurzu, nemůže pořadač modelů automaticky aktualizovat `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8f51d-199">Since the view doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="8f51d-200">Namísto použití pořadače modelů k aktualizaci `CourseAssignments` navigační vlastnosti to uděláte v nové `UpdateInstructorCourses` metodě.</span><span class="sxs-lookup"><span data-stu-id="8f51d-200">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="8f51d-201">Proto je nutné vyloučit `CourseAssignments` vlastnost z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-201">Therefore, you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="8f51d-202">To nevyžaduje žádné změny kódu, který volá, `TryUpdateModel` protože používáte přetížení, které vyžaduje explicitní schválení a `CourseAssignments` není v seznamu zahrnutí.</span><span class="sxs-lookup"><span data-stu-id="8f51d-202">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the overload that requires explicit approval and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="8f51d-203">Pokud nebyla vybrána žádná zaškrtávací políčka, kód v rámci `UpdateInstructorCourses` inicializuje `CourseAssignments` navigační vlastnost s prázdnou kolekcí a vrátí:</span><span class="sxs-lookup"><span data-stu-id="8f51d-203">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

<span data-ttu-id="8f51d-204">Kód pak projde všemi kurzy v databázi a zkontroluje každý kurz s těmi, které jsou aktuálně přiřazeny k instruktorovi, a k těm, které byly vybrány v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8f51d-204">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the view.</span></span> <span data-ttu-id="8f51d-205">Aby bylo možné efektivně vyhledávat, jsou tyto dvě kolekce uloženy v `HashSet` objektech.</span><span class="sxs-lookup"><span data-stu-id="8f51d-205">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="8f51d-206">Pokud je vybráno zaškrtávací políčko pro kurz, ale kurz není v `Instructor.CourseAssignments` navigační vlastnosti, kurz se přidá do kolekce v navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8f51d-206">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

<span data-ttu-id="8f51d-207">Pokud není vybrané zaškrtávací políčko pro kurz, ale kurz je v `Instructor.CourseAssignments` navigační vlastnosti, kurz se odebere z navigační vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8f51d-207">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a><span data-ttu-id="8f51d-208">Aktualizace zobrazení instruktora</span><span class="sxs-lookup"><span data-stu-id="8f51d-208">Update the Instructor views</span></span>

<span data-ttu-id="8f51d-209">V *zobrazeních/instruktorech/upravit. cshtml*přidejte pole **kurzů** s polem zaškrtávacích políček přidáním následujícího kódu hned za `div` prvky pro pole **Office** a před `div` prvkem pro tlačítko **Uložit** .</span><span class="sxs-lookup"><span data-stu-id="8f51d-209">In *Views/Instructors/Edit.cshtml*, add a **Courses** field with an array of check boxes by adding the following code immediately after the `div` elements for the **Office** field and before the `div` element for the **Save** button.</span></span>

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="8f51d-210">Když kód vložíte v aplikaci Visual Studio, mohou být zalomení řádků změněny způsobem, který kód přerušuje.</span><span class="sxs-lookup"><span data-stu-id="8f51d-210">When you paste the code in Visual Studio, line breaks might be changed in a way that breaks the code.</span></span> <span data-ttu-id="8f51d-211">Pokud kód po vložení vypadá jinak, stiskněte klávesovou zkratku CTRL + Z, aby bylo automatické formátování vráceno zpět.</span><span class="sxs-lookup"><span data-stu-id="8f51d-211">If the code looks different after pasting, press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="8f51d-212">Tím dojde k odstranění konců řádků, aby vypadaly jako v tomto příkladu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-212">This will fix the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="8f51d-213">Odsazení nemusí být dokonalé, ale `@:</tr><tr>` řádky,, a `@:<td>` `@:</td>` `@:</tr>` musí být na jednom řádku, jak je znázorněno, nebo se zobrazí chyba za běhu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-213">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown or you'll get a runtime error.</span></span> <span data-ttu-id="8f51d-214">Po vybrání bloku nového kódu stiskněte klávesu Tabulátor třikrát, aby se nový kód pořádek nastavil s existujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="8f51d-214">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="8f51d-215">Tento problém je opravený v aplikaci Visual Studio 2019.</span><span class="sxs-lookup"><span data-stu-id="8f51d-215">This problem is fixed in Visual Studio 2019.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

<span data-ttu-id="8f51d-216">Tento kód vytvoří tabulku HTML, která má tři sloupce.</span><span class="sxs-lookup"><span data-stu-id="8f51d-216">This code creates an HTML table that has three columns.</span></span> <span data-ttu-id="8f51d-217">V každém sloupci je zaškrtávací políčko následované titulkem, který se skládá z čísla a názvu kurzu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-217">In each column is a check box followed by a caption that consists of the course number and title.</span></span> <span data-ttu-id="8f51d-218">Všechna zaškrtávací políčka mají stejný název ("selectedCourses"), který informuje pořadač modelů o tom, že se mají považovat za skupinu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-218">The check boxes all have the same name ("selectedCourses"), which informs the model binder that they're to be treated as a group.</span></span> <span data-ttu-id="8f51d-219">Atribut Value každé zaškrtávací políčko je nastaven na hodnotu `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="8f51d-219">The value attribute of each check box is set to the value of `CourseID`.</span></span> <span data-ttu-id="8f51d-220">Po zveřejnění stránky předává pořadač modelu pole do kontroleru, který se skládá z `CourseID` hodnot pouze u zaškrtávacích políček, která jsou vybrána.</span><span class="sxs-lookup"><span data-stu-id="8f51d-220">When the page is posted, the model binder passes an array to the controller that consists of the `CourseID` values for only the check boxes which are selected.</span></span>

<span data-ttu-id="8f51d-221">Když jsou tato zaškrtávací políčka zpočátku vykreslena, jsou pro kurzy přiřazené instruktorem zkontrolovány atributy, které je vyberou (zobrazí zaškrtnutí).</span><span class="sxs-lookup"><span data-stu-id="8f51d-221">When the check boxes are initially rendered, those that are for courses assigned to the instructor have checked attributes, which selects them (displays them checked).</span></span>

<span data-ttu-id="8f51d-222">Spusťte aplikaci, vyberte kartu **instruktory** a klikněte na tlačítko **Upravit** na instruktorovi, aby se zobrazila stránka pro **Úpravy** .</span><span class="sxs-lookup"><span data-stu-id="8f51d-222">Run the app, select the **Instructors** tab, and click **Edit** on an instructor to see the **Edit** page.</span></span>

![Stránka pro úpravu instruktorů s kurzy](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="8f51d-224">Změňte některá přiřazení kurzů a klikněte na Uložit.</span><span class="sxs-lookup"><span data-stu-id="8f51d-224">Change some course assignments and click Save.</span></span> <span data-ttu-id="8f51d-225">Změny, které provedete, se projeví na stránce indexu.</span><span class="sxs-lookup"><span data-stu-id="8f51d-225">The changes you make are reflected on the Index page.</span></span>

> [!NOTE]
> <span data-ttu-id="8f51d-226">Postup, který je zde k dispozici pro úpravu dat kurzu instruktora, funguje dobře, pokud existuje omezený počet kurzů.</span><span class="sxs-lookup"><span data-stu-id="8f51d-226">The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="8f51d-227">Pro kolekce, které jsou mnohem větší, by se vyžadovalo jiné uživatelské rozhraní a odlišná metoda aktualizace.</span><span class="sxs-lookup"><span data-stu-id="8f51d-227">For collections that are much larger, a different UI and a different updating method would be required.</span></span>

## <a name="update-delete-page"></a><span data-ttu-id="8f51d-228">Aktualizace stránky pro odstranění</span><span class="sxs-lookup"><span data-stu-id="8f51d-228">Update Delete page</span></span>

<span data-ttu-id="8f51d-229">V *InstructorsController.cs*odstraňte `DeleteConfirmed` metodu a vložte následující kód na místo.</span><span class="sxs-lookup"><span data-stu-id="8f51d-229">In *InstructorsController.cs*, delete the `DeleteConfirmed` method and insert the following code in its place.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

<span data-ttu-id="8f51d-230">Tento kód provede následující změny:</span><span class="sxs-lookup"><span data-stu-id="8f51d-230">This code makes the following changes:</span></span>

* <span data-ttu-id="8f51d-231">Načítá Eager pro `CourseAssignments` navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="8f51d-231">Does eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="8f51d-232">Je nutné zahrnout tento, nebo EF neznáte související `CourseAssignment` entity a nebude je odstraňovat.</span><span class="sxs-lookup"><span data-stu-id="8f51d-232">You have to include this or EF won't know about related `CourseAssignment` entities and won't delete them.</span></span> <span data-ttu-id="8f51d-233">Abyste se vyhnuli nutnosti jejich čtení, můžete v databázi nakonfigurovat kaskádové odstranění.</span><span class="sxs-lookup"><span data-stu-id="8f51d-233">To avoid needing to read them here you could configure cascade delete in the database.</span></span>

* <span data-ttu-id="8f51d-234">Pokud je instruktor, který má být odstraněn, přiřazen jako správce jakékoli oddělení, odebere z těchto oddělení přiřazení instruktora.</span><span class="sxs-lookup"><span data-stu-id="8f51d-234">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="add-office-location-and-courses-to-create-page"></a><span data-ttu-id="8f51d-235">Přidání umístění Office a kurzů k vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="8f51d-235">Add office location and courses to Create page</span></span>

<span data-ttu-id="8f51d-236">V *InstructorsController.cs*odstraňte metody HttpGet a HTTPPOST `Create` a pak na jejich místo přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="8f51d-236">In *InstructorsController.cs*, delete the HttpGet and HttpPost `Create` methods, and then add the following code in their place:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

<span data-ttu-id="8f51d-237">Tento kód se podobá tomu, co jste viděli pro `Edit` metody s tím rozdílem, že se nevybraly žádné kurzy.</span><span class="sxs-lookup"><span data-stu-id="8f51d-237">This code is similar to what you saw for the `Edit` methods except that initially no courses are selected.</span></span> <span data-ttu-id="8f51d-238">Metoda HttpGet `Create` volá metodu, `PopulateAssignedCourseData` protože by mohly být vybrány kurzy, ale za účelem poskytnutí prázdné kolekce pro `foreach` smyčku v zobrazení (jinak kód zobrazení by vyvolal výjimku s hodnotou null).</span><span class="sxs-lookup"><span data-stu-id="8f51d-238">The HttpGet `Create` method calls the `PopulateAssignedCourseData` method not because there might be courses selected but in order to provide an empty collection for the `foreach` loop in the view (otherwise the view code would throw a null reference exception).</span></span>

<span data-ttu-id="8f51d-239">Metoda HttpPost `Create` přidá každý vybraný kurz do `CourseAssignments` vlastnosti navigace před tím, než zkontroluje chyby ověřování a přidá nového instruktora do databáze.</span><span class="sxs-lookup"><span data-stu-id="8f51d-239">The HttpPost `Create` method adds each selected course to the `CourseAssignments` navigation property before it checks for validation errors and adds the new instructor to the database.</span></span> <span data-ttu-id="8f51d-240">Kurzy se přidávají i v případě, že dojde k chybám modelu, takže když dojde k chybám modelu (například uživatel zaznamená neplatné datum) a stránka se znovu zobrazí s chybovou zprávou, všechny vybrané kurzy se automaticky obnoví.</span><span class="sxs-lookup"><span data-stu-id="8f51d-240">Courses are added even if there are model errors so that when there are model errors (for an example, the user keyed an invalid date), and the page is redisplayed with an error message, any course selections that were made are automatically restored.</span></span>

<span data-ttu-id="8f51d-241">Všimněte si, že aby bylo možné do navigační vlastnosti přidat kurzy, `CourseAssignments` musíte tuto vlastnost inicializovat jako prázdnou kolekci:</span><span class="sxs-lookup"><span data-stu-id="8f51d-241">Notice that in order to be able to add courses to the `CourseAssignments` navigation property you have to initialize the property as an empty collection:</span></span>

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

<span data-ttu-id="8f51d-242">Jako alternativu k tomu, že se jedná o kód kontroleru, to můžete provést v modelu instruktoru změnou vlastnosti getter na automaticky vytvořit kolekci, pokud neexistuje, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="8f51d-242">As an alternative to doing this in controller code, you could do it in the Instructor model by changing the property getter to automatically create the collection if it doesn't exist, as shown in the following example:</span></span>

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

<span data-ttu-id="8f51d-243">Pokud `CourseAssignments` tuto vlastnost upravíte tímto způsobem, můžete v řadiči odebrat explicitní inicializační kód vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8f51d-243">If you modify the `CourseAssignments` property in this way, you can remove the explicit property initialization code in the controller.</span></span>

<span data-ttu-id="8f51d-244">V *zobrazení/instruktor/vytvořit. cshtml*, přidejte textové pole umístění kanceláře a zaškrtávací políčka pro kurzy před tlačítkem Odeslat.</span><span class="sxs-lookup"><span data-stu-id="8f51d-244">In *Views/Instructor/Create.cshtml*, add an office location text box and check boxes for courses before the Submit button.</span></span> <span data-ttu-id="8f51d-245">Jako v případě stránky pro úpravy [opravte formátování, pokud aplikace Visual Studio přeformátuje kód při jeho vložení](#notepad).</span><span class="sxs-lookup"><span data-stu-id="8f51d-245">As in the case of the Edit page, [fix the formatting if Visual Studio reformats the code when you paste it](#notepad).</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

<span data-ttu-id="8f51d-246">Otestujte spuštěním aplikace a vytvořením instruktora.</span><span class="sxs-lookup"><span data-stu-id="8f51d-246">Test by running the app and creating an instructor.</span></span>

## <a name="handling-transactions"></a><span data-ttu-id="8f51d-247">Zpracování transakcí</span><span class="sxs-lookup"><span data-stu-id="8f51d-247">Handling Transactions</span></span>

<span data-ttu-id="8f51d-248">Jak je vysvětleno v [kurzu CRUD](crud.md), Entity Framework implicitně implementuje transakce.</span><span class="sxs-lookup"><span data-stu-id="8f51d-248">As explained in the [CRUD tutorial](crud.md), the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="8f51d-249">V případě scénářů, kde potřebujete větší kontrolu – například pokud chcete zahrnout operace provedené mimo Entity Framework v transakci – viz [transakce](/ef/core/saving/transactions).</span><span class="sxs-lookup"><span data-stu-id="8f51d-249">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="8f51d-250">Získání kódu</span><span class="sxs-lookup"><span data-stu-id="8f51d-250">Get the code</span></span>

[<span data-ttu-id="8f51d-251">Stažení nebo zobrazení dokončené aplikace.</span><span class="sxs-lookup"><span data-stu-id="8f51d-251">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="8f51d-252">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8f51d-252">Next steps</span></span>

<span data-ttu-id="8f51d-253">V tomto kurzu jste:</span><span class="sxs-lookup"><span data-stu-id="8f51d-253">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8f51d-254">Stránky přizpůsobených kurzů</span><span class="sxs-lookup"><span data-stu-id="8f51d-254">Customized Courses pages</span></span>
> * <span data-ttu-id="8f51d-255">Stránka pro úpravu přidaných instruktorů</span><span class="sxs-lookup"><span data-stu-id="8f51d-255">Added Instructors Edit page</span></span>
> * <span data-ttu-id="8f51d-256">Přidání kurzů pro úpravu stránky</span><span class="sxs-lookup"><span data-stu-id="8f51d-256">Added courses to Edit page</span></span>
> * <span data-ttu-id="8f51d-257">Aktualizace stránky pro odstranění</span><span class="sxs-lookup"><span data-stu-id="8f51d-257">Updated Delete page</span></span>
> * <span data-ttu-id="8f51d-258">Přidání umístění Office a kurzů k vytvoření stránky</span><span class="sxs-lookup"><span data-stu-id="8f51d-258">Added office location and courses to Create page</span></span>

<span data-ttu-id="8f51d-259">Přejděte k dalšímu kurzu, kde se dozvíte, jak zpracovávat konflikty souběžnosti.</span><span class="sxs-lookup"><span data-stu-id="8f51d-259">Advance to the next tutorial to learn how to handle concurrency conflicts.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="8f51d-260">Zpracování konfliktů souběžnosti</span><span class="sxs-lookup"><span data-stu-id="8f51d-260">Handle concurrency conflicts</span></span>](concurrency.md)
